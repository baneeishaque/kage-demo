# Documentation

Field notes for operating [Kage](https://github.com/karim852/Kage-DFIR-toolkit) — the
open-source Windows DFIR triage console — written from a real demo run on macOS plus the
tool's own source and documentation. Nothing here is a substitute for the tool's own
[README](https://github.com/karim852/Kage-DFIR-toolkit#readme).

| Document                             | Answers                                                              |
| ------------------------------------ | -------------------------------------------------------------------- |
| [`architecture.md`](architecture.md) | How the console, the eleven-step chain, the score, and the seals fit together |
| [`trade-offs.md`](trade-offs.md)     | What the tool does not do, and the limits worth knowing first        |
| [`demo-mode.md`](demo-mode.md)       | What the synthetic intrusion contains and what a demo run proves     |
| [`headless-ssh.md`](headless-ssh.md) | Running it elevated and headless on a remote Windows host over SSH   |
| [`explainers/`](explainers/)         | Line-by-line walkthroughs of the chain and seals, the score, and the server |

## Ground rules used throughout

- **Authorization first.** Kage collects evidence and runs scanners; use it only on hosts you
  own or have written permission to examine.
- **The console has no authentication.** It binds to `127.0.0.1` by design. Reach it through
  an SSH tunnel; never bind it to `0.0.0.0` or expose the port.
- **Evidence stays where it was collected.** The chain writes to the host's own `evidence/`
  and `output/` folders; copying it elsewhere is a deliberate, documented step.
