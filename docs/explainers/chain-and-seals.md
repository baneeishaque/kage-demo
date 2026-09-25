# Explainer: the chain and its seals

A walkthrough of `dfirconsole/pipeline.py` — how the eleven-step chain executes, how each
completed step is sealed, and why a run survives a browser refresh, a dropped tunnel, or a
closed laptop. Read [`../architecture.md`](../architecture.md) first for the step table and the
seal examples this code produces.

Line references are against Kage 1.5.0.

## `StepState` — one record per step

```python
@dataclass
class StepState:
    id: str
    label: str
    hint: str
    ...
    status: str = PENDING
    started: float | None = None
    ended: float | None = None
    message: str = ""
    seal: str = ""
    seal_basis: str = ""
    artifacts: list[str] = field(default_factory=list)
```

- **`seal` and `seal_basis` are a pair.** A seal that does not say what it covers is a claim
  you cannot audit; the two fields travel together from `_seal()` to the report. The UI prints
  `seal 31d1991a65598de8 — content of hayabusa-output.csv`, never the hash alone.
- **`artifacts` is the input to sealing.** A step that wants its outputs protected must name
  them here; the seal function can only hash what the step declares.
- **Timestamps are stored raw.** `started`/`ended` are `float | None`, not preformatted
  strings — presentation is the serializer's job.

```python
def public(self) -> dict:            # :82
    ...
    "duration": round((self.ended - self.started), 1)
    if self.started and self.ended
    else None,
```

- **One serialization shape.** `public()` feeds the websocket snapshot and both report
  endpoints. There is no second code path that could disagree about what a step looks like.
- **Duration is computed at read time and is `None` until the step ends.** A running step shows
  no duration rather than a wrong one; rounding to tenths matches the `SEALED 0.1s` lines.

## `sha256_file` — hashing without loading the file

```python
def sha256_file(path: Path, chunk: int = 1024 * 1024) -> str:   # :102
    digest = hashlib.sha256()
    with path.open("rb") as handle:
        while block := handle.read(chunk):
            digest.update(block)
    return digest.hexdigest()
```

- **1 MiB chunks, constant memory.** A CyLR collection easily reaches several gigabytes; the
  hash must not require the file to fit in RAM.
- **The walrus loop stops on the empty read**, so a file that is still being written is hashed
  up to the last complete block — good enough for change detection, and it never blocks on
  EOF semantics.

## `_define_steps` — the chain as data

```python
specs = [                                                        # :131
    ("workspace", "Prepare the workspace", "Folder tree (tools, evidence, output)", "", False, False),
    ("defender", "Exclude the folder from Defender", "Add-MpPreference -ExclusionPath", "powershell Add-MpPreference -ExclusionPath <workspace>", True, True),
    ...
]
for step_id, label, hint, command, admin, optional in specs:
    self.steps[step_id] = StepState(
        id=step_id, label=label, hint=hint, command=command,
        admin=admin, optional=optional,
        default_on=step_id != "yara",
    )
    self.order.append(step_id)
```

- **The chain is a list of tuples, not eleven methods.** Order, labels, hints, the elevation
  flag, and the default tick state all live in one readable table; `self.order` is the
  execution order every consumer iterates.
- **`admin` and `optional` are different questions.** `admin` says the step needs an elevated
  process; `optional` says a failure should skip the step rather than stop the chain. THOR is
  `admin=True, optional=True` — it needs elevation *and* it may legitimately be absent.
- **`default_on=step_id != "yara"`** keeps the YARA scan unticked, with the rationale in a
  comment above it: a full THOR run is the slowest step and assumes a licensed tool. Every
  other step runs when you click **Run the chain**.
- **The labels here are the labels in the seals.** `✔ Analyse the timeline SEALED 0.1s` is
  this string, not a UI translation — so a seal line and the chain table can always be matched.

## `_seal` — what a seal covers

```python
def _seal(self, step: StepState) -> tuple[str, str]:             # :1268
    digest = hashlib.sha256()
    hashed: list[str] = []
    for name in step.artifacts:
        for folder in (self.settings.output_dir, self.settings.evidence_dir,
                       self.settings.tools_dir):
            candidate = folder / name
            if candidate.is_file():
                digest.update(sha256_file(candidate).encode())
                hashed.append(name)
                break

    if hashed:
        return digest.hexdigest()[:16], "content of " + ", ".join(hashed)

    digest.update(f"{step.id}|{step.started}|{step.message}".encode())
    return digest.hexdigest()[:16], "execution record (no file produced)"
```

- **Content seals fold the *hashes* of the artefacts into one digest.** `sha256_file(...)` is
  encoded and fed to the outer digest, so the seal is stable regardless of file order or size
  and still changes if any artefact changes.
- **The folder search order is deliberate:** output, then evidence, then tools — first hit
  wins, and the loop `break`s. A file name is expected to live in exactly one of the three.
- **The fallback seal covers the execution record** — step id, start time, result message. A
  step that produced no file (the Defender exclusion, for example) still gets a seal, but the
  basis says `execution record (no file produced)` instead of implying content it never had.
- **Sixteen hex characters** (64 bits) is the deliberate output size: long enough that
  accidental collisions are irrelevant for change detection, short enough to read aloud or
  compare by eye in a report.

## `run` — the executor

```python
async def run(self, step_ids: list[str]) -> None:                # :1308
    if self.running:
        raise RuntimeError("A run is already in progress.")
    handlers = self._handlers()
    unknown = [s for s in step_ids if s not in handlers]
    if unknown:
        raise ValueError(f"Unknown steps: {unknown}")
    ...
    for step_id in step_ids:
        step = self.steps[step_id]
        step.status = RUNNING
        step.started = time.time()
        ...
        try:
            message = await handlers[step_id]()
            step.status = DONE
            step.message = message
            step.seal, step.seal_basis = self._seal(step)
        except StepFailure as exc:
            step.status = SKIPPED if step.optional else FAILED
        except asyncio.CancelledError:
            step.status = FAILED
            step.message = "Interrupted by the analyst."
            raise
        except Exception as exc:
            step.status = FAILED
            step.message = f"{exc.__class__.__name__}: {exc}"
        finally:
            step.ended = time.time()
            self._push_step(step)

        if step.status == FAILED and not step.optional:
            self.log("Chain stopped: the next step depends on this one.",
                     level="error", step=step_id)
            break
```

- **Guards run before anything mutates state**: a second concurrent run raises, and an unknown
  step id fails fast instead of silently doing nothing.
- **Three exception classes, three behaviours.** `StepFailure` is a *business* error — the
  message is written for the analyst and shown as-is; optional steps become `SKIPPED`, required
  steps `FAILED`. `CancelledError` is the analyst pressing Stop: the step is marked failed with
  that exact wording and the exception is re-raised so the task actually terminates. Anything
  else is a programming error and keeps its raw class name and message (the `noqa` comment
  says why: a sanitized message would hide the bug).
- **Sealing happens inside the success path**, immediately after the handler returns — a step
  that failed is never sealed, so the report cannot imply a failed step produced trustworthy
  output.
- **`finally` pushes the step state on every exit path**, so the UI's chain-of-custody panel is
  never left showing a step as running after it ended.
- **Dependency honesty**: a required step's failure stops the chain with an explicit log line.
  Optional failures (THOR missing, enrichment without keys) carry on — matching the
  `trade-offs.md` promise that the chain degrades gracefully where it can, and says so where it
  cannot.

## Lifecycle: `start`, `cancel`, `reset`

```python
def start(self, step_ids: list[str]) -> None:                    # :1363
    self._task = asyncio.create_task(self.run(step_ids))

def cancel(self) -> bool:                                        # :1366
    if self._task and not self._task.done():
        self._task.cancel()
        self.log("Stop requested.", level="warn")
        return True
    return False

def reset(self) -> None:                                         # :1373
    for step in self.steps.values():
        step.status = PENDING
        step.message = ""
        step.seal = step.seal_basis = ""
        ...
```

- **`start` returns immediately.** The HTTP handler does not await the chain; the run lives in
  an asyncio task, which is exactly why closing the browser tab does not stop it.
- **`cancel` returns a boolean instead of raising** — the HTTP layer answers
  `{"cancelled": true|false}` without exception handling.
- **`reset` clears the seals along with the statuses.** A stale seal from a previous run is
  worse than no seal; the reset makes the report honestly empty.

## How the pieces compose

```text
server.py  ──POST /api/run──►  Case.start()
                                 │  asyncio.create_task
                                 ▼
                              run(step_ids)
                                 │  per step: handler() → _seal() → push
                                 ▼
              bus.emit(...) ──► websocket /ws ──► the browser (a view, never the engine)
                                 │
                                 ▼
                    snapshot() / report.json / report.html — seals included
```
