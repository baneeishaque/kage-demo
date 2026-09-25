# Architecture

Kage (影, "shadow") is a **triage console**, not another collector. It orchestrates the
forensic tools that already earned their reputation, streams every command live to a browser,
and seals each completed step so the result is defensible. Version 1.5.0 is a single Python
process — FastAPI + uvicorn — that serves a local web UI on `127.0.0.1:8787`.

## From launch to report

```text
python -m dfirconsole
  └─ server.py          argparse: --host 127.0.0.1 --port 8787 --demo
     └─ Case            one workspace per run: tools/ · evidence/ · output/
        └─ pipeline.py  the eleven-step chain, streamed over a websocket
           └─ report    HTML + JSON, seals included
```

The browser is a view, not the engine: the analysis runs server-side, the websocket pushes
progress, and a refresh never loses work.

## The eleven steps

Registered in `dfirconsole/pipeline.py`, in execution order. "Admin" marks steps that need an
elevated process; "Default" marks whether the step runs when you click **Run the chain**
(every step is tickable, and any step can be replayed alone with a double-click).

| #  | Step id     | Label                             | What it does                                                     | Admin | Default |
| -- | ----------- | --------------------------------- | ---------------------------------------------------------------- | ----- | ------- |
| 01 | `workspace` | Prepare the workspace             | Creates the folder tree (tools, evidence, output); reports free space | no | on |
| 02 | `defender`  | Exclude the folder from Defender  | `Add-MpPreference -ExclusionPath <workspace>` so the collection is not quarantined mid-run | yes | on |
| 03 | `tools`     | Locate the tooling                | Finds CyLR, Hayabusa, and THOR Lite in their folders; fetches the first two from GitHub when missing | no | on |
| 04 | `collect`   | Collect the artefacts             | CyLR: event logs, registry, prefetch, `$MFT`, and more            | yes | on |
| 05 | `system`    | Capture the system context        | Accounts, connections and listening ports, disk-root anomalies, logging coverage | yes | on |
| 06 | `rules`     | Update the Sigma rules            | `hayabusa update-rules`                                           | no | on |
| 07 | `timeline`  | Build the timeline                | Sigma correlation over the collected EVTX (Hayabusa 3 `csv-timeline` / 4 `dfir-timeline`, auto-detected) | no | on |
| 08 | `analyse`   | Analyse the timeline              | Statistics, alert families, indicator extraction, the score       | no | on |
| 09 | `yara`      | YARA scan (THOR Lite)             | Scans the collected artefacts by default; scope of your choosing  | yes | off |
| 10 | `enrich`    | Enrich the indicators             | VirusTotal + AbuseIPDB lookups (optional keys)                    | no | on |
| 11 | `ai`        | Write the summary                 | Report written up by a language model, or a local fallback        | no | on |

The first four steps are the base — once those are sealed, the rest are yours to tick. The
YARA scan stays unticked by default because a full THOR run is the slowest step.

## The tools it orchestrates

- **[CyLR](https://github.com/orlikoski/CyLR)** — the collector. Kage never substitutes
  another binary: no CyLR, no collection (the step fails and says so).
- **[Hayabusa](https://github.com/Yamato-Security/hayabusa)** — the timeline engine; its
  Sigma rule set is updated in step 06.
- **[THOR Lite](https://github.com/NextronSystems/thor-lite)** — the YARA scanner. It is
  distributed after registration with Nextron, so Kage cannot download it; without it the
  step is skipped and the chain carries on.
- **VirusTotal + AbuseIPDB** — external reputation for extracted indicators.
- **A language model** (optional) — writes the summary; without a key, a deterministic local
  report is produced instead.

## The score

The triage score (0–100) is four independent, capped axes — the formula lives in
`dfirconsole/timeline.py`:

| Axis              | Max | How it is computed                                                        |
| ----------------- | --- | ------------------------------------------------------------------------- |
| Severity          | 45  | Log-compressed weighted alert volume — `6.5 × √raw`, capped               |
| Kill chain        | 25  | Breadth of the attack across tactics, with decisive tactics weighted      |
| Reputation        | 20  | Only externally confirmed indicators count (`10 × malicious + 3 × suspicious`) |
| Corroboration     | 10  | Agreement between independent sources (+6 / +4 per match), capped         |

Two properties matter more than the number:

- **No single source can dominate.** A noisy rule firing hundreds of times saturates its own
  axis and cannot reach a multi-stage verdict by volume alone.
- **Confidence is separate from severity.** It counts how many independent sources agree — a
  high score from Sigma alone reads as a strong lead, never a confirmation.

## Seals and chain of custody

Every completed step is sealed with a SHA-256, and the seal **states what it covers**:

```text
✔ Analyse the timeline              SEALED 0.1s
  seal 31d1991a65598de8 — content of hayabusa-output.csv
✔ Exclude the folder from Defender  SEALED 6.2s
  seal 8f2c04b71ae93d55 — execution record (no file produced)
```

When a step produced files, the seal is the hash **of their contents** — rerun the step later
and you have proof the artefact was not altered. When it produced none, the seal covers only
the execution record, and says so rather than implying more. The report carries the whole
chain, each step with its seal and what that seal covers.

## The system context (step 05)

Event logs miss the present tense. The system step captures it, read-only:

- local accounts and their status,
- connections and listening ports matched to owning processes,
- anomalies at the disk root (unexpected executables and folders),
- and the machine's logging coverage — because a verdict is only worth what the host agreed
  to record.

## Outputs

- `output/` — the report (HTML and JSON; the JSON is the complete state, seals included).
- `evidence/` — the collection, the timeline CSV, the THOR log; treat it as evidence.
- The session log — every command, verbatim, streamed as it ran.

Configuration lives in `config.json` and `apikeys.env` (both optional); the workspace,
case reference, analyst name, and keys are editable in the UI's Settings drawer.
