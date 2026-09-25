# Explainer: the headless server

A walkthrough of `dfirconsole/server.py` — the FastAPI process that serves the console, why it
binds to loopback only, and what makes the headless-over-SSH workflow possible. Read
[`../headless-ssh.md`](../headless-ssh.md) for the operational procedure this code enables,
and [`../trade-offs.md`](../trade-offs.md) for the no-authentication decision.

Line references are against Kage 1.5.0.

## The security posture, stated in the file

```python
"""Local server for the analysis workstation.

Binds to 127.0.0.1 by default: the tool handles evidence artefacts and API
keys, and has no business being exposed on the network.
"""
```

- **The docstring is the design decision.** Anyone tempted to add `--host 0.0.0.0` support
  reads the reason not to before touching the argparse block at the bottom of the same file.
- **The default is safe by construction**: `main()`'s `--host` default is `127.0.0.1`, so the
  dangerous configuration requires a deliberate, visible flag.

## One process, one case

```python
bus = EventBus()                                                 # :28
settings = Settings.load()
case = Case(settings, bus)
```

- **Module-level singletons, deliberately.** Kage's scope is one host per run, one workspace,
  no case management (see `trade-offs.md`); the server process *is* the case. A per-request
  case registry would imply a multi-case feature the tool does not have.
- **The event bus is separate from the case** so the console log and the websocket stream
  outlive any single step — and `case` stays the state machine explained in
  [`chain-and-seals.md`](chain-and-seals.md).

```python
def _attach_logfile() -> None:                                   # :33
    safe = "".join(c if c.isalnum() or c in "-_" else "_" for c in settings.case_name)
    bus.attach_file(settings.output_dir / f"{safe}-console.log")
```

- **The case name is sanitized before it becomes a filename** — path separators and shell
  metacharacters become `_`, so a case named `../../evil` writes `.._.._evil-console.log`
  inside `output/`, not somewhere else.
- **One log per case under `output/`.** The session log is an evidence-adjacent artifact and
  lands with the report, not in a temp directory.

## The app and its request models

```python
app = FastAPI(title="Kage DFIR Toolkit", docs_url=None, redoc_url=None)   # :41
```

- **`docs_url=None, redoc_url=None`** removes the auto-generated interactive API docs. On a
  loopback-only tool this is small hardening — but it also means the HTTP surface is exactly
  the routes below, with nothing extra to reason about.

```python
class RunRequest(BaseModel):                                     # :44
    steps: list[str] = Field(default_factory=list)

class SettingsRequest(BaseModel):                                # :48
    workspace: str | None = None
    case_name: str | None = None
    ...
```

- **`RunRequest.steps` defaults to empty** — and the `/api/run` handler reads empty as "the
  whole chain". The client can send a subset without a second endpoint.
- **`SettingsRequest` is all-optional by design.** Combined with `exclude_none=True` below,
  a partial settings update cannot accidentally reset fields the client did not send.

## The browser is a view, not the engine

```python
@app.get("/{page}")                                              # :79
async def page(page: str) -> FileResponse:
    """Views are rendered in the browser; the server returns the same page.

    Switching views never loses the analysis: it is held server-side and
    reloaded from /api/state on every mount.
    """
    if f"/{page}" not in ROUTES:
        raise HTTPException(status_code=404, detail="Unknown page.")
    return FileResponse(WEB_DIR / "index.html")
```

- **Every view is the same `index.html`.** Navigation is client-side; the server's only job is
  to hand over the app shell — and to 404 anything outside the known `ROUTES` tuple.
- **The docstring is the architecture statement:** the analysis lives in `case`, so a refresh
  or a view switch is a re-read of `/api/state`, never a restart. This is exactly why a
  dropped SSH tunnel during a multi-gigabyte collection does not lose the run.

```python
@app.get("/api/state")                                           # :98
async def state() -> JSONResponse:
    return JSONResponse(case.snapshot())
```

- **One snapshot endpoint feeds the whole UI.** `snapshot()` is the same serialization used in
  the websocket `hello` and the final `run finished` event — one shape, three consumers.

## Driving the chain

```python
@app.post("/api/run")                                            # :121
async def run(payload: RunRequest) -> JSONResponse:
    if case.running:
        raise HTTPException(status_code=409, detail="A run is already in progress.")
    steps = payload.steps or case.order
    unknown = [s for s in steps if s not in case.steps]
    if unknown:
        raise HTTPException(status_code=400, detail=f"Unknown steps: {', '.join(unknown)}")
    case.start(steps)
    return JSONResponse({"started": steps})
```

- **409, not a queue.** A second run while one is active is a client error, not a feature —
  two concurrent collections in one workspace would corrupt the case.
- **`payload.steps or case.order`** is the "whole chain" default; the response echoes exactly
  what started, so the UI can render the truth rather than its own guess.
- **Validation happens twice** (here and inside `Case.run`): the HTTP layer returns a clean
  400, and the state machine keeps its own guard for programmatic callers.
- **`case.start(steps)` returns immediately** — the request that starts an hours-long
  collection finishes in milliseconds, and the progress arrives over the websocket.

```python
@app.post("/api/cancel")                                         # :133
async def cancel() -> JSONResponse:
    return JSONResponse({"cancelled": case.cancel()})

@app.post("/api/reset")                                          # :138
async def reset() -> JSONResponse:
    if case.running:
        raise HTTPException(status_code=409, detail="Stop the run before resetting.")
    case.reset()
```

- **Cancel is idempotent and honest**: it answers `{"cancelled": false}` when nothing was
  running instead of pretending.
- **Reset refuses while running.** Clearing seals and state under a live collection would
  produce a report that cannot be trusted; the 409 makes the operator stop first.

## Settings and log attachment

```python
@app.post("/api/settings")                                       # :108
async def update_settings(payload: SettingsRequest) -> JSONResponse:
    changes = payload.model_dump(exclude_none=True)
    for key, value in changes.items():
        setattr(settings, key, value)
    settings.save()
    if {"case_name", "workspace"} & changes.keys():
        _attach_logfile()
    bus.log("Configuration saved.", level="ok")
    bus.emit("settings", settings=settings.public())
```

- **`exclude_none=True` is the partial-update contract**: only the fields the client sent are
  applied, then the whole settings object is persisted.
- **Changing the case name or workspace re-attaches the logfile**, so the console log always
  sits in the *current* output directory rather than the one the process started in.
- **A settings save is announced on the bus** — the UI and the log see it like any other event,
  which keeps the audit trail complete.

## The artifacts: log, JSON, HTML

```python
@app.get("/api/console.log")                                     # :147
async def console_log() -> Response:
    ...
    headers={"Content-Disposition": f'attachment; filename="{path.name}"'},

@app.get("/api/report.json")                                     # :159
async def report_json() -> Response:
    body = json.dumps({
        "case": settings.case_name,
        "analyst": settings.analyst,
        "generated": datetime.now(timezone.utc).isoformat(),
        "steps": [case.steps[s].public() for s in case.order],
        "timeline": case.report,
        "thor": case.thor,
        "ai": case.ai,
    }, ensure_ascii=False, indent=2)
```

- **Downloads are attachments, not pages.** The console log and both reports carry
  `Content-Disposition: attachment` — the browser saves evidence artifacts, it does not render
  them inline.
- **The JSON report is the complete state**: case identity, analyst, generation timestamp,
  every step with its seal and seal basis, the timeline, the THOR results, the summary. This is
  the artifact to hash and archive.
- **`ensure_ascii=False`** keeps non-Latin evidence (file paths, usernames) readable in the
  export instead of `\uXXXX` escapes; `indent=2` makes it diffable.
- **The HTML report is rendered server-side** from the same `case` object by
  `report_module.render(settings, case)` — one source of truth, two renderings.

## The websocket: reconnect-proof streaming

```python
@app.websocket("/ws")                                            # :189
async def websocket(ws: WebSocket) -> None:
    await ws.accept()
    queue = bus.subscribe()
    try:
        await ws.send_json({"kind": "hello", "snapshot": case.snapshot(),
                            "backlog": bus.backlog()})
        while True:
            event = await queue.get()
            await ws.send_json(event)
    except (WebSocketDisconnect, RuntimeError, asyncio.CancelledError):
        pass  # a closed tab is not an error
    finally:
        bus.unsubscribe(queue)
        with suppress(Exception):
            await ws.close()
```

- **`hello` carries the full snapshot plus the backlog**, so a client that connects (or
  reconnects after a tunnel drop) immediately sees the complete state *and* the events it
  missed. There is no "reload to catch up" step.
- **Each subscriber gets its own queue** (`bus.subscribe()`), so a slow or dead client cannot
  stall the chain — the pump simply stops consuming for that one queue.
- **A closed tab is explicitly not an error** (comment included), and the final `close()` is
  wrapped in `suppress(Exception)`: disconnects are normal in this workflow, and a traceback
  per disconnect would drown the console log.
- **`unsubscribe` in `finally`** is the leak guard: queues do not accumulate across reconnects
  during a long collection.

## Startup: the argparse block that makes headless possible

```python
def main() -> None:                                              # :213
    parser = argparse.ArgumentParser(
        description="Kage DFIR Toolkit — triage CyLR / Hayabusa / THOR Lite")
    parser.add_argument("--host", default="127.0.0.1")
    parser.add_argument("--port", type=int, default=8787)
    parser.add_argument("--demo", action="store_true", help="Force demonstration mode")
    args = parser.parse_args()

    if args.demo:
        settings.demo_mode = True
    ...
    print(f"{APP_FULL_NAME} {__version__}")
    print(f"  code      {APP_DIR}")
    print(f"  workspace {settings.workspace}")
    print(f"  open      http://{args.host}:{args.port}")
    uvicorn.run(app, host=args.host, port=args.port, log_level="warning")
```

- **Three flags, no config file required.** `--host` exists for completeness but its default
  is the safe value; `--port` allows a second instance; `--demo` forces the synthetic chain.
- **The banner prints the exact URL** (`open http://127.0.0.1:8787`) — in the headless
  workflow, that line in the session log is the definitive "it is up" signal the SSH operator
  checks before opening the tunnel.
- **`log_level="warning"`** keeps uvicorn's request chatter out of the console log; the
  application's own bus events are the narrative.

## How the headless workflow composes

```text
Task Scheduler (/RL HIGHEST, hidden) ──► python -m dfirconsole  (on the host)
                                             │  uvicorn 127.0.0.1:8787
                                             ▼
      your machine ──ssh -L 8787:127.0.0.1:8787──► host loopback
             │
             ▼
      browser at http://127.0.0.1:8787 ──ws /ws──► live stream
             │                                      │
             └── the case, the evidence, the seals ── stay on the host's disk
```

No desktop session, no window on anyone's screen, no evidence leaving the host — the server
file is where that workflow stops being a trick and becomes an architecture.
