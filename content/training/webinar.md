# Webinar — Kage headless DFIR triage

A 90-minute live online session (60 minutes talk + live demo, then a **30-minute Q&A session**
to close). Free, recorded, and pitched at software developers with about two years of
experience and no DFIR background. The goal: by the end, every attendee can run a full
synthetic triage chain on their own laptop and explain what a seal proves.

## Learning outcomes

By the end of the webinar, attendees can:

1. Explain what a DFIR triage is and why chain of custody decides whether it is defensible.
2. Run the eleven-step demo chain and read the report (verdict, breakdown, confidence).
3. Explain what a SHA-256 seal covers, and why the basis string matters.
4. Describe the four score axes and why each is capped.
5. Describe the headless-over-SSH workflow — and its two hard rules (loopback only,
   authorized hosts only).

## Before the session (instructor checklist)

1. **Environment** — clone the tool, install requirements, confirm `python3 -m dfirconsole
   --demo` serves `http://127.0.0.1:8787` on the presenting machine. Have the 37-locale video
   open in a second tab (pre-work material).
2. **Reset the console** — run one demo chain to warm caches, then press **Reset** so the live
   demo starts clean.
3. **Slides** — 20–25 slides; every claim in them appears in `docs/architecture.md` (the
   guide is the source of truth, not the slides).
4. **Recording** — start the recorder before the welcome; confirm the console UI is legible at
   the streamed resolution (zoom the browser to ~125%).
5. **Q&A tooling** — a question queue (chat or Q&A panel) with one moderator who groups
   questions during the talk; the last slide shows the queue is open.
6. **Fallbacks** — if the live demo fails, the recorded demo segments (screen captures of the
   same run) are queued in the deck; if the network fails, the article link is read aloud.

## Run sheet (90 minutes)

| Time          | Segment                        | Content                                                                 |
| ------------- | ------------------------------ | ----------------------------------------------------------------------- |
| 0:00–0:05     | Welcome and agenda             | Who this is for; what "demo mode" means; Q&A at the end                 |
| 0:05–0:12     | The story                      | The finance-laptop constraint: triage without disrupting the owner      |
| 0:12–0:22     | The problem                    | Tool sprawl, integration by analyst, unprovable custody                 |
| 0:22–0:35     | One chain, not ten tools       | The eleven steps; **live demo: run the chain in demo mode**             |
| 0:35–0:48     | Seals that state their basis   | **Live demo: read the SEALED lines; verify one seal**                   |
| 0:48–0:58     | A score you can argue with     | Four capped axes; confidence separate; verdict wording                  |
| 0:58–1:10     | Headless without disrupting work | **Demo: schtasks + SSH tunnel** (recorded segment on a Windows VM)    |
| 1:10–1:18     | Rehearse, then trust           | Demo mode details; the 37-locale video as the meta-proof                |
| 1:18–1:20     | Wrap and CTA                   | The article, the guide repository, the free course; how to ask questions |
| 1:20–1:50     | **Q&A session**                | Live questions; pre-prepared bank below; close with the safety rules    |

## Live demo 1 — run the chain (0:22–0:35)

Step by step, narrating each action:

1. Show the console at `http://127.0.0.1:8787` with the chain panel: eleven steps, first four
   marked as the base, YARA unticked by default. Say why: a full THOR run is the slowest step
   and assumes a licensed tool.
2. Tick **Demonstration mode** in Settings, save, and point out the log line that confirms it.
3. Click **Run the chain**. Narrate the stream: workspace → defender → tools → collect →
   system → rules → timeline → analyse → enrich → ai. Point out that the browser is a view:
   refresh the page mid-run and nothing is lost.
4. When the run finishes, show the verdict line: `80 / 100 · CONFIDENCE HIGH · 441 events
   analysed`. Explain the 441: a 20-event scenario plus 420 noise events, plus the header.
5. Open the report (HTML) and show the same numbers appear there — one state, two renderings.

## Live demo 2 — seals and verification (0:35–0:48)

1. Scroll the chain panel to the SEALED lines and read both examples aloud:
   `seal 31d1991a65598de8 — content of hayabusa-output.csv` and
   `seal 8f2c04b71ae93d55 — execution record (no file produced)`.
2. Explain the contract: the seal either covers file contents, or it says it only covers the
   execution record. A bare hash is decoration; a basis string is a claim you can audit.
3. Verify one by hand: hash `output/hayabusa-output.csv` (or the file named on screen) with
   the platform's SHA-256 tool and compare the first 16 hex characters with the seal.
4. State the limits honestly: a seal proves the artefact has not changed since sealing — it
   does not prove the collection was complete or correct. That is what the trade-offs doc is
   for.

## Live demo 3 — headless over SSH (0:58–1:10)

This segment runs as a recorded screen capture against a Windows VM (reproduce live only if a
VM is available):

1. On the host, over SSH, create and run the scheduled task:

   ```cmd
   schtasks /Create /TN KageHeadless /TR "cmd /c cd /d C:\Kage && python -m dfirconsole" /SC ONCE /ST 23:59 /RL HIGHEST /F
   schtasks /Run /TN KageHeadless
   ```

   Explain `/RL HIGHEST`: elevated, hidden, no UAC prompt — the machine's owner never sees a
   window.
2. Verify on the host: `curl.exe -s -o NUL -w "%{http_code}" http://127.0.0.1:8787` → `200`.
3. From the presenter's laptop, open the tunnel:
   `ssh -N -o ServerAliveInterval=30 -o ExitOnForwardFailure=yes -L 8787:127.0.0.1:8787 user@host`
   and open `http://127.0.0.1:8787`.
4. Make the boundary explicit: the console has **no authentication by design**, so the tunnel
   *is* the security boundary — never bind `0.0.0.0`, keep the tunnel open only while working.
5. Show teardown: `schtasks /End`, `schtasks /Delete`, and the two forgotten cleanups — the
   Defender exclusion from step 02, and the SSH key installed for the investigation.

## Pre-prepared Q&A bank

Use these when the room is quiet; they are the questions every cohort asks.

1. **"Is this legal on any machine?"** No. Written authorization and an agreed scope come
   first, always. Demo mode exists so you can learn everything without touching a host.
2. **"Why does the console have no login?"** Because it binds to loopback and is meant to be
   reached through an SSH tunnel; adding auth would add a credential surface the tool does not
   need. The trade-off is documented — and the rule is never `--host 0.0.0.0`.
3. **"Why is the YARA step unticked?"** A full THOR Lite run is the slowest step and requires
   registration with Nextron; the chain carries on without it.
4. **"What does the seal actually prove?"** That the artefact's content (or the execution
   record) is unchanged since sealing. Rerun the hash later and compare.
5. **"Why 45/25/20/10?"** Each axis is capped so no single source can dominate; the caps sum
   to 100. Severity is log-compressed so volume alone cannot buy a verdict.
6. **"What is confidence, in one sentence?"** How many independent sources agree, plus whether
   the host's logging coverage is sufficient — evidence quality, not severity.
7. **"Can I run this on Linux or macOS?"** The console runs, but the full chain is
   Windows-first; elsewhere you get demo mode (which is ideal for learning).
8. **"Does the AI write the report by itself?"** It writes the summary if a provider key is
   configured; otherwise a deterministic local report is produced. The score and seals never
   depend on it.
9. **"How large is a real collection?"** Several gigabytes — CyLR's own preflight says so.
   Step 04 dominates the wall-clock time; the console streams progress throughout.
10. **"Can it do multiple hosts?"** No — one host per run, one workspace. That is the scope:
    a fast, defensible first pass, not a fleet platform.
11. **"What happens if a step fails?"** Optional steps (THOR, enrichment) are skipped and the
    chain carries on; a required step's failure stops the chain with an explicit log line.
12. **"How do I learn more?"** The article for the system design, the guide repository for the
    full procedures, the line-by-line explainers for the code, and the free self-paced course
    for hands-on practice.

## After the session

1. Stop the recording; publish it with chapters matching the run sheet, and pin the article
   link in the description.
2. Send the follow-up message: recording link, article link with the webinar UTM, the guide
   repository, and the free-course sign-up.
3. Share the feedback form (three questions: pace, depth, what to cover next).
4. Note every unanswered question and answer it in the community thread — that thread becomes
   the next cohort's Q&A bank.
5. Post the webinar highlights as a short-form clip (see
   [`../funnel-medium/reels.md`](../funnel-medium/reels.md), Reel 3).
