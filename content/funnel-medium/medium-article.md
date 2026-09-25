# The host you must not disturb: headless DFIR triage, sealed step by step

*Eleven steps, SHA-256 seals that state what they cover, and a score you can argue with — run
elevated and headless on a machine that has to stay usable.*

***

There is a machine every responder eventually meets. It is the finance director's laptop, or
the build server with a queue, or the workstation of the person who just reported a phishing
email. It needs a first-pass triage — today. And it is a machine someone is using.

The usual answer is a shoebox of tools. One collects artefacts, another builds a timeline, a
third scans for malware, a fourth enriches indicators, and a fifth, if you are lucky, writes
something you can hand to a client. Every hop loses context. Nothing proves the evidence
stayed untouched. And someone has to sit in front of the machine while it runs — which is
exactly the disruption you were trying to avoid.

I wrote this guide around a console that answers those constraints up front: **Kage** (影,
"shadow"), an open-source, MIT-licensed Windows DFIR triage console. One chain instead of ten
tools. A hash-sealed chain of custody. A score you can audit axis by axis. And a headless mode
that lets the machine stay in use while you work from your own.

Everything below was verified against version 1.5.0 and a real demo run; the guide, the
line-by-line code explainers, and the publishing kit live here:

**Guide + kit: [github.com/baneeishaque-ompventure/kage-demo](https://github.com/baneeishaque-ompventure/kage-demo)**

**The tool: [github.com/karim852/Kage-DFIR-toolkit](https://github.com/karim852/Kage-DFIR-toolkit)**

Stack: Python 3.10+, FastAPI + uvicorn serving a local web UI, orchestrating CyLR
(collection), Hayabusa with its Sigma rule set (timeline), THOR Lite (YARA), VirusTotal and
AbuseIPDB (enrichment), and an optional language model for the written summary. Windows-first;
the demo mode runs everywhere.

## One chain, not ten tools

The most common failure mode in triage is integration by analyst: the human becomes the glue
between five tools with five output formats and no shared custody. Bugs — and worse,
custody gaps — live in the seams.

Kage's answer is boring and effective: the chain is data, not tribal knowledge. Eleven steps
in a fixed order, each one a row in a single table in `dfirconsole/pipeline.py`:

1. **Prepare the workspace** — folder tree, free-space report.
2. **Exclude the folder from Defender** — so the collection is not quarantined mid-run.
3. **Locate the tooling** — finds CyLR, Hayabusa, and THOR Lite; fetches the first two when
   missing.
4. **Collect the artefacts** — CyLR: event logs, registry, prefetch, `$MFT`, and more.
5. **Capture the system context** — accounts, connections and listening ports, disk-root
   anomalies, logging coverage.
6. **Update the Sigma rules** — `hayabusa update-rules`.
7. **Build the timeline** — Sigma correlation over the collected EVTX.
8. **Analyse the timeline** — statistics, alert families, indicator extraction, the score.
9. **YARA scan (THOR Lite)** — unticked by default, because a full THOR run is the slowest
   step.
10. **Enrich the indicators** — VirusTotal + AbuseIPDB, optional keys.
11. **Write the summary** — a language model, or a deterministic local fallback.

The first four steps are the base — once those are sealed, the rest are yours to tick. Every
command streams live to the browser as it runs, and the analysis runs server-side: refresh the
page mid-collection and nothing is lost.

That last property is not a UI nicety. It is the difference between a tool you babysit and a
tool you drive. The browser is a view, never the engine.

## Every step is sealed — and the seal says what it covers

This is the decision that separates a script folder from something defensible. When a step
completes, it is sealed with a SHA-256, and the seal **states its basis**:

```text
✔ Analyse the timeline              SEALED 0.1s
  seal 31d1991a65598de8 — content of hayabusa-output.csv
✔ Exclude the folder from Defender  SEALED 6.2s
  seal 8f2c04b71ae93d55 — execution record (no file produced)
```

When a step produced files, the seal is the hash of their contents — rerun the hash a week
later and you have proof the artefact was not altered. When it produced none, the seal covers
only the execution record, and **says so** rather than implying more than it can prove.

The implementation is small enough to read in one sitting:

```python
def _seal(self, step: StepState) -> tuple[str, str]:
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

Three details carry the design:

- **The seal folds the hashes of the artefacts into one digest**, so it is stable regardless of
  file order and still changes if any artefact changes.
- **The fallback is explicit.** A step that produced no file still gets a seal — over its step
  id, start time, and result message — and the basis string says
  `execution record (no file produced)`.
- **Sixteen hex characters** (64 bits) is a deliberate size: long enough that accidental
  collisions are irrelevant for change detection, short enough to read aloud in a report
  review.

The full walkthrough — the state machine, the executor, the three exception classes — is in
the guide's [`docs/explainers/chain-and-seals.md`](https://github.com/baneeishaque-ompventure/kage-demo/blob/main/docs/explainers/chain-and-seals.md).

## A score you can argue with

"Threat score: 87" is a number nobody can audit. Kage's score is four independent axes, each
capped, summed to a 0–100 that always ships with its breakdown:

```python
SEVERITY_CAP, CHAIN_CAP, REPUTATION_CAP, CORROBORATION_CAP = 45, 25, 20, 10

# -- severity: log-compressed weighted volume
raw = sum(LEVEL_WEIGHT[level] * count for level, count in self.levels.items())
severity = min(self.SEVERITY_CAP, round(6.5 * (raw ** 0.5))) if raw else 0

# -- kill chain: breadth of the attack, with decisive tactics weighted
decisive = set(self.tactics) & self.DECISIVE_TACTICS
chain = min(self.CHAIN_CAP, 3 * len(tactics) + 3 * len(decisive))

# -- reputation: only externally confirmed indicators count
reputation = min(self.REPUTATION_CAP, 10 * malicious + 3 * suspicious)
```

- **Severity is log-compressed** (`6.5 × √raw`): a thousand noisy events add far less than the
  count suggests, and a single noisy rule saturates its own axis without buying a verdict.
- **Kill chain weights decisive tactics** — discovery alone is an administrator; discovery
  plus credential access is not.
- **Reputation counts only externally confirmed indicators.** With no API keys it contributes
  zero, and the breakdown says so.
- **Corroboration rewards agreement** between independent sources (+6 for YARA detections, +4
  for live connections to public hosts).

Two properties matter more than the total. First, **no single source can dominate** — every
axis is capped, so volume cannot fake a multi-stage intrusion. Second, **confidence is a
separate statement about evidence quality**: it counts how many independent sources agree, and
`high` additionally requires the host's logging coverage to be at least 45%. A verdict is only
worth what the machine agreed to record.

The same discipline shows up in the verdict wording. 80/100 at high confidence is "Compromise
confirmed by multiple sources". 80/100 from the Sigma timeline alone is "Compromise highly
likely — corroboration still limited". The same number, two honest sentences. A triage tool
that never says "Nothing conclusive" is a tool nobody trusts the second time.

## Headless, without disrupting work

This is the workflow that makes the constraint disappear. The console is a local web app
(`127.0.0.1:8787`), so it needs **no desktop session** — a scheduled task can start it hidden,
elevated, and it survives your disconnect.

The obstacle is elevation. An SSH session on Windows is non-interactive: UAC cannot prompt, so
`runas`-style elevation is impossible — and collection, the Defender exclusion, and THOR all
need administrator rights. The clean answer is a Task Scheduler task with `/RL HIGHEST`:

```cmd
schtasks /Create /TN KageHeadless /TR "cmd /c cd /d C:\Kage && python -m dfirconsole" /SC ONCE /ST 23:59 /RL HIGHEST /F
schtasks /Run /TN KageHeadless
```

Then from your own machine:

```bash
ssh -N -o ServerAliveInterval=30 -o ExitOnForwardFailure=yes -L 8787:127.0.0.1:8787 user@host
```

Open `http://127.0.0.1:8787` locally and the entire console is there. What each piece buys
you:

- **`/RL HIGHEST`** runs with the account's highest privileges — an elevated process with no
  UAC prompt, even though the SSH session that created the task was not elevated.
- **Task Scheduler runs it hidden**: no window appears on anyone's desktop. The machine stays
  in use while the collection runs.
- **The tunnel is the security boundary.** The console ships no authentication, tokens, or TLS
  — by design (see the trade-offs below). `-L 8787:127.0.0.1:8787` forwards your local port to
  the host's loopback, and the evidence never moves: collection, timeline, report, and seals
  all stay on the host's disk, with only the live stream travelling.

When you are done, copy the results off deliberately over the same SSH channel
(`scp -r user@host:C:/Kage/output ./kage-output`) and document the hand-off. Two cleanups
people forget: step 02's Defender exclusion persists after the run (remove it when policy
requires), and the SSH key you installed was for this investigation — remove it if the access
was one-time.

The full procedure — OpenSSH installation, the administrator vs standard-user authorized-keys
split, the task variants, the warm-up checklist, teardown, and a troubleshooting table — is in
the guide's `docs/headless-ssh.md`. The server-side architecture that makes it possible is in
`docs/explainers/headless-server.md`.

## Rehearse before you touch anything

Demo mode runs the **entire eleven-step chain** against a synthetic intrusion instead of the
host. The scenario (`dfirconsole/demo.py`) is a full intrusion story on a host named
`DESKTOP-R7K2QX1`, seeded deterministically so every demo run tells the same tale: a
phishing-style chain from a document to `cmd.exe` to encoded PowerShell, a malware hash
matching the EICAR test signature, Defender disabled, an AMSI bypass, LSASS access for
credential theft, persistence, command-and-control to `45.155.205.233:8443` with a JA3
fingerprint matching a known C2 profile, shadow copies deleted, boot configuration tampering,
the event log cleared, and a multi-gigabyte exfiltration pattern.

Around the story sit **420 noise events** — ordinary Windows activity — so the timeline is a
realistic haystack, not a highlight reel. A full demo run lands at:

```text
80 / 100          CONFIDENCE HIGH · 441 events analysed
```

Nothing on your machine is read, scanned, or changed: no evidence collection, no THOR, no
Defender exclusion, no admin. On macOS and Linux the demo is the default mode; on Windows it
is one checkbox in Settings.

That makes demo mode safe on a production laptop, in a workshop, or on the Mac you are using
to read this — and it is how you learn where the buttons are so the real triage is not your
first time in the UI.

## The test suite is the specification

115 tests exercise the pipeline and the risk model — including the same deterministic timeline
builder the demo uses, so the synthetic intrusion is a fixture, not a screenshot. The claims
in this article are the claims the suite enforces:

- the chain seals every completed step, and the seal basis matches what the step produced;
- the four axes stay inside their caps, and confidence tracks independent sources;
- the demo scenario is reproducible from its seed, noise included;
- the report surfaces carry the seals through to HTML and JSON.

## The trade-offs, honestly

Every architecture is a set of accepted costs. These are the ones worth knowing before
pointing Kage at a host:

| Decision | You gain | You pay |
| --- | --- | --- |
| Loopback-only, no authentication | No credential surface; the tunnel is the boundary | Anyone who reaches the port can drive the chain — never bind `0.0.0.0` |
| Windows-first chain | Defender, CyLR, EVTX/Hayabusa, THOR all first-class | On macOS/Linux you get demo mode, not collection |
| Four capped axes | Volume cannot fake a verdict; every number is auditable | A genuinely massive single-source incident still reads as a lead |
| Content seals with explicit fallback | Every step is provable, or says it is not | A seal over an execution record proves less than a content hash |
| Optional external services | No API keys, no failures — enrichment and AI degrade gracefully | Without keys, reputation contributes zero and the summary is local |
| Single host, single case | A fast, local, defensible first pass | No fleet view, no case management, no server-side database |

None of these are oversights. Each one lives in the guide's `docs/trade-offs.md` with its
reasoning, so the next engineer can disagree on purpose instead of by accident.

## Results

- 11 chain steps, one workspace, one report — no tool sprawl
- Every completed step sealed, with the seal stating what it covers
- A 0–100 score built from four capped axes, published with its breakdown and a separate
  confidence line
- Headless operation over SSH: no desktop session, no disruption, evidence never leaves the
  host
- A deterministic demo — 80/100, confidence high, 441 events analysed — that touches nothing
- 115 tests green, MIT-licensed, version 1.5.0

And the meta-proof: this guide's repository ships a video pipeline that narrates the
walkthrough in **20 languages** and subtitles it in **37** — English, Hindi, Malayalam, Tamil,
Kannada, Bengali, Arabic, and more — produced entirely from demo mode. The system the video
describes is the system that produced the video, without a single byte of real evidence.

## Steal this

The repository is public and MIT-licensed, with the architecture guide, the trade-offs, and
line-by-line explanations next to the code:

**Guide + kit: [github.com/baneeishaque-ompventure/kage-demo](https://github.com/baneeishaque-ompventure/kage-demo)**

If you take one thing from this article, take the seal rule: **make every claim state its
basis.** A hash that covers "the content of hayabusa-output.csv" survives a report review; a
bare number does not. The tools are the easy part. Custody is what decides whether the other
90% holds.

***

Kage is created by k0r1m and released under the MIT license. This write-up is an independent
guide, not affiliated with or endorsed by the author. Point it only at hosts you own or are
explicitly authorized to examine — written authorization and an agreed scope come first, every
time.

*If this saved you a triage integration project, star the repository — it is how other
responders find it.*
