# Trade-offs

Honesty about limits is what separates architecture from advocacy. These are the constraints
worth knowing before pointing Kage at a host — each is a deliberate design choice, not an
oversight.

## The score is a triage heuristic, not proof

The 0–100 score compresses four capped axes into one number so that a first responder can
triage at a glance. It is not a forensic conclusion:

- Severity is **log-compressed** (`6.5 × √raw`): a thousand noisy events add far less than
  the count suggests.
- Reputation counts only **externally confirmed** indicators; with no API keys it contributes
  zero.
- Corroboration rewards agreement between independent sources — but independent sources can
  still agree on a benign pattern.

Treat a high score as "look here first", and read the per-axis breakdown and the confidence
line before drawing conclusions.

## Windows-first

The full chain is built for Windows 10/11/Server — Defender exclusion, CyLR collection, the
EVTX/Hayabusa pipeline, THOR. A Linux version is in progress upstream. On macOS and Linux the
console runs in **demo mode by default** (`demo_mode = not IS_WINDOWS`), which is ideal for
learning the interface but does not collect from the host.

## Some steps depend on third-party realities

- **THOR Lite requires registration** with Nextron Systems and cannot be auto-downloaded. No
  THOR, no YARA scan: the step is skipped and the chain carries on (it is unticked by
  default anyway).
- **CyLR is the only collector.** No silent substitution — if `CyLR.exe` is missing, the
  collection step fails and says so.
- **Hayabusa changes between major versions** (`csv-timeline` in v3, `dfir-timeline` in v4).
  Kage queries the binary's help output and adapts; pinned versions are avoided because the
  download would break the day upstream moves on.
- **API keys are optional.** VirusTotal/AbuseIPDB enrichment and the language-model summary
  degrade gracefully: without keys you get the local deterministic report, not an error.

## The console has no authentication — by design

Kage binds to `127.0.0.1` and ships no login, tokens, or TLS. Anyone who can reach the port
can drive the chain. This is acceptable **only** because loopback is unreachable from the
network:

- Never launch with `--host 0.0.0.0`.
- Reach a remote console through an SSH tunnel (`docs/headless-ssh.md`).
- The corollary: a shared multi-user host means any local user could reach the console while
  it runs. On an investigation host, that is part of the threat model.

## Elevation is required for the full chain

Defender exclusion (step 02), CyLR's raw NTFS access (step 04), and THOR (step 09) need an
elevated process. Started unelevated, Kage warns and the dependent steps fail — it does not
silently degrade. See `docs/headless-ssh.md` for the scheduled-task pattern that elevates
without a UAC prompt.

## Demo mode is synthetic, and says so

Demo mode never reads, scans, or changes the host. It is a rehearsal of the **workflow** — the
evidence, timeline, and system context are generated. Do not present a demo run as a finding;
the log lines mark every simulated step (`Demonstration mode: …`).

## Collection is large and takes time

A CyLR collection "easily reaches several GB" (the tool's own preflight says so). On a slow
disk or a busy host, step 04 dominates the run. The console streams progress and survives a
browser refresh or a dropped SSH tunnel, but the run itself is not instant.

## Single-host, single-case

Kage triages **one host per run** into one workspace, with no multi-host fleet view, no case
management, and no server-side database. That is the scope: a fast, local, defensible first
pass — not an enterprise DFIR platform.
