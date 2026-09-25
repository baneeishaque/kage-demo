# Demo mode

Demo mode runs the **entire eleven-step chain** against a synthetic intrusion instead of the
host. It is the fastest way to learn the console, rehearse a run, and produce screenshots and
recordings without touching a machine — and on macOS and Linux it is the default.

## Enabling it

Three equivalent switches:

| Context            | Switch                                                              |
| ------------------ | ------------------------------------------------------------------- |
| Command line       | `python -m dfirconsole --demo` (forces demo mode on any platform)   |
| Automatic          | `demo_mode = not IS_WINDOWS` in `dfirconsole/config.py` — macOS/Linux default |
| Console UI         | Settings → **Demonstration mode — generates fake artefacts instead of running the collection** |

The UI checkbox is the one to know: tick it, save, and the next **Run the chain** is a
rehearsal — even on Windows.

## What the synthetic intrusion contains

The scenario (`dfirconsole/demo.py`) is a full intrusion story on a host named
`DESKTOP-R7K2QX1`, seeded deterministically (`seed=7`) so every demo run tells the same tale:

- a phishing-style chain from a document to `cmd.exe` to **encoded PowerShell**,
- a malware hash matching the **EICAR** test signature,
- **Defender disabled** and an **AMSI bypass**,
- **LSASS access** for credential theft,
- persistence, and **command-and-control** to `45.155.205.233:8443` with a JA3 fingerprint
  matching a known C2 profile,
- lateral-movement attempts, **shadow copies deleted** (`vssadmin`), boot configuration
  tampering (`bcdedit`), and the event log cleared,
- staging with `7z` and a 4.4 GB exfiltration pattern.

Around the story sit **420 noise events** — ordinary Windows activity — so the timeline is a
realistic haystack, not a highlight reel. The demo timeline builder is the same one the tests
use (`build_demo_timeline(target, seed=7, noise=420)`).

## What actually runs — and what is simulated

| Step        | In demo mode                                                          |
| ----------- | --------------------------------------------------------------------- |
| `workspace` | Real: the folder tree is created                                      |
| `defender`  | Real command path, but no collection depends on it in demo            |
| `tools`     | Simulated: "no download" — no binaries are fetched                    |
| `collect`   | Simulated: synthetic artefacts replace the CyLR run                   |
| `system`    | Simulated: `demo_system()` returns the generated context              |
| `rules`     | Simulated: "rule update simulated"                                    |
| `timeline`  | Simulated: the synthetic timeline is written                          |
| `analyse`   | Real: the analyser, the score, and the indicator extraction run on the synthetic data |
| `yara`      | Optional and unticked by default; without THOR it is skipped          |
| `enrich`    | Runs, but the synthetic indicators are not real reputation lookups    |
| `ai`        | Local fallback report unless a provider key is configured             |

Every simulated step says so in the log (`Demonstration mode: …`), and the chain still seals
its steps — you can watch the chain-of-custody mechanics end to end.

## What a demo run looks like

The example run in the tool's own README lands at:

```text
80 / 100          CONFIDENCE HIGH · 441 events analysed
```

— 441 events = the 20-event scenario plus 420 noise events, plus the header. The score
breakdown (Severity / Kill chain / Reputation / Corroboration), the alert families, the
indicator list, the ATT&CK mapping, and the summary are all populated, and the HTML/JSON
report is written to `output/`.

## What it never does

- No host reads: no EVTX, registry, prefetch, or `$MFT` access.
- No scans: no THOR, no YARA, no network reputation lookups against real indicators.
- No changes: no Defender exclusion is needed, nothing is quarantined, no services touched.
- No admin: the demo runs unelevated on any OS.

That makes demo mode safe on a production laptop, in a workshop, or on the Mac you are using
to read this.

## Good uses

1. **Rehearse before the real run** — learn where the buttons are so the real triage is not
   your first time in the UI.
2. **Capture content** — screenshots and screen recordings of a fully populated console, with
   no evidence-handling concerns. (This kit's video was produced this way.)
3. **Teach** — the synthetic scenario is a ready-made intrusion with a known ground truth.
4. **Smoke-test a deployment** — verify the console starts, streams, and reports on a new
   machine without collecting anything.
