# Free training — Kage Fundamentals (self-paced)

A free, self-paced course in five modules, 6–8 hours total. It starts at "what is triage?"
and ends with a verified, sealed, headless-ready workflow on the learner's own laptop. Every
module has objectives, a short video, guided steps with exact commands, a lab with a
checkpoint, and a quiz with answers. No DFIR background required — just a terminal and about
two years of engineering experience.

**The deal:** demo mode is the entire classroom. Nothing in this course touches a real host,
so there is nothing to authorize and nothing to break.

## Course map

| Module | Title                          | Time   | Lab output                                   |
| ------ | ------------------------------ | ------ | -------------------------------------------- |
| M1     | What triage is, and where Kage fits | 60 min | A written definition of "defensible triage" |
| M2     | The chain, step by step        | 90 min | A finished demo chain + annotated step list  |
| M3     | Seals and chain of custody     | 90 min | A hand-verified seal and a limit statement   |
| M4     | The score and confidence       | 90 min | A one-paragraph triage note                  |
| M5     | Headless over SSH              | 120 min | A tunneled console + a hand-off checklist    |

## Module 1 — What triage is, and where Kage fits (60 min)

**Objectives:** define triage and chain of custody; explain why tool sprawl is a custody
problem; describe what Kage orchestrates.

1. **Read** `docs/architecture.md` (the guide repository) — the chain table, the score table,
   and the seal examples. 20 minutes.
2. **Watch** the 37-locale video's English cut (the `content/youtube/` pipeline's output, or
   the published upload) — the whole system in about three minutes.
3. **Write**, in your own words, a definition of *defensible triage*: what would you need to
   prove to a client after examining a host? Keep it to three sentences.
4. **Lab:** list the five tools a typical ad-hoc triage uses (collection, timeline, YARA,
   enrichment, summary) and one thing that gets lost between each pair.
5. **Checkpoint:** your definition names both *evidence* and *custody*.

**Quiz:** (a) Why is "the analyst is the integration layer" a risk? (b) Name the two
properties of a good triage tool from this module. *Answers: (a) context and custody are lost
at each manual hop, and the analyst becomes the weakest link at the worst moment; (b) one
chain with live streaming, and seals that state their basis.*

## Module 2 — The chain, step by step (90 min)

**Objectives:** install and run the tool; execute the eleven-step chain in demo mode; map each
step to what it produces.

1. **Install** (15 min):

   ```bash
   git clone https://github.com/karim852/Kage-DFIR-toolkit.git
   cd Kage-DFIR-Toolkit
   python3 -m pip install -r requirements.txt
   python3 -m dfirconsole --demo
   ```

   Open `http://127.0.0.1:8787`.
2. **Tour** (10 min): the chain panel, the Settings drawer, the report links. Note which
   steps are marked admin and which is unticked by default (YARA) — and why.
3. **Run** (15 min): tick Demonstration mode, set a case reference, run the chain. Watch every
   SEALED line appear.
4. **Annotate** (30 min): for each of the eleven steps, write one line: what it produces, and
   whether it is required or optional.
5. **Lab:** refresh the browser mid-run; confirm nothing is lost. Then press **Reset** and
   explain what was cleared.
6. **Checkpoint:** a completed run and an eleven-row annotated table.

**Quiz:** (a) Which step dominates wall-clock time on a real host, and why? (b) What happens
when an optional step fails? *Answers: (a) step 04, collection — CyLR reads gigabytes of raw
artefacts; (b) it is skipped, the chain carries on, and the log says so.*

## Module 3 — Seals and chain of custody (90 min)

**Objectives:** explain what a seal covers; verify one by hand; state its limits honestly.

1. **Read** `docs/explainers/chain-and-seals.md` (30 min) — focus on `_seal()` and the two
   basis strings.
2. **Verify** (20 min): pick a seal whose basis is `content of <file>`, hash the file yourself
   (`shasum -a 256` on macOS, `sha256sum` on Linux), and compare the first 16 characters.
3. **Break it** (10 min): modify the file, hash again, observe the mismatch; then Reset and
   rerun the demo to restore a clean state.
4. **Reason** (20 min): write one sentence for each of these — what a content seal proves;
   what the `execution record` seal proves; what neither proves.
5. **Checkpoint:** a matching hash and the three sentences.

**Quiz:** (a) Why does the fallback seal exist at all? (b) Why 16 hex characters?
*Answers: (a) so every completed step is provable-or-explicitly-limited, never silently
unproven; (b) 64 bits is far beyond change-detection needs while staying human-readable in a
report.*

## Module 4 — The score and confidence (90 min)

**Objectives:** read the four-axis breakdown; explain log compression; interpret confidence
and verdict wording.

1. **Read** `docs/explainers/scoring.md` (30 min) — the caps, the compression, the confidence
   ladder.
2. **Compute** (20 min): take the demo breakdown and recompute the severity axis by hand
   (`6.5 × √raw`, capped at 45). Confirm your number matches the UI.
3. **Interpret** (20 min): write the one-paragraph triage note for the demo run — verdict,
   confidence, strongest axis, weakest axis, next action.
4. **Argue** (20 min): rewrite the verdict for the same score at `low` confidence. Compare
   with the tool's wording. Which one would you send to a client?
5. **Checkpoint:** a paragraph that never claims more than the confidence supports.

**Quiz:** (a) Why is severity log-compressed? (b) What two conditions produce `high`
confidence? *Answers: (a) so alert volume alone cannot buy a verdict; (c) at least three
independent sources agreeing, and log coverage of at least 45% (or unknown).*

## Module 5 — Headless over SSH (120 min)

**Objectives:** explain why loopback-only is the design; run the scheduled-task pattern; drive
the console through a tunnel; hand off evidence cleanly.

1. **Read** `docs/headless-ssh.md` (30 min) end to end — including the troubleshooting table
   and the four safety rules.
2. **Set up a Windows host** (30 min): a personal VM is fine (Hyper-V, VirtualBox, or a cloud
   instance). Install OpenSSH Server, place your key (mind the
   `administrators_authorized_keys` split), and verify `ssh user@host whoami`.
3. **Go headless** (20 min): create and run the scheduled task with `/RL HIGHEST`; verify
   `200` from `curl.exe` on the host; open the tunnel from your machine.
4. **Survive a disconnect** (10 min): start a chain, close the tab, drop the tunnel, reconnect
   — the run continues; refresh and the state is complete.
5. **Hand off** (20 min): `scp -r` the output, verify one hash against the report seal, then
   tear down — task deleted, Defender exclusion removed, one-time SSH key removed.
6. **Checkpoint:** a tunneled run, a verified copy, and a completed teardown checklist.

**Quiz:** (a) Why can't a Windows SSH session elevate with `runas`? (b) What is the security
boundary of a headless deployment? *Answers: (a) the session is non-interactive — UAC cannot
prompt; a Task Scheduler task with `/RL HIGHEST` is the clean elevation path; (b) the SSH
tunnel — the console itself has no authentication, so it must stay on loopback.*

## Final assessment

A take-home task: run a fresh demo chain, write a one-page triage note (verdict, confidence,
two findings, one axis you trust least, next command on a real host), and record a two-minute
screen capture of one headless run through a tunnel. Pass = the note never overclaims and the
capture shows the evidence staying on the host.

## After the course

- The article (`content/funnel-medium/medium-article.md`) is the next read — it explains the
  design decisions behind everything you just ran.
- The paid course continues into multi-scenario practice and a full capstone triage; the
  hands-on session is the one-day version.
- Keep the two hard rules wherever you take this: **loopback only** and **authorized hosts
  only**.
