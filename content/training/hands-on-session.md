# Hands-on session — Kage in practice

A four-hour instructor-led workshop. Participants run the full workflow themselves: the demo
chain on their own laptop, seal verification by hand, score interpretation, and — on a
purpose-built Windows VM — a real chain driven headless over SSH. Every exercise is
step-by-step; every lab has a checkpoint so the instructor can see who is behind.

## Format

| Item            | Value                                                        |
| --------------- | ------------------------------------------------------------ |
| Length          | 4 hours (with two 10-minute breaks)                          |
| Class size      | 8–20 participants (pairs work well for labs 5–7)             |
| Delivery        | In person or virtual with breakout rooms                     |
| Materials       | This run sheet; the guide repository cloned on every machine |
| Windows VM      | One per pair (or per participant), trainer-owned, pre-seeded |
| Prerequisites   | Laptop with Python 3.10+, a browser, an SSH client           |

## Agenda

| Time      | Segment                                        | Labs        |
| --------- | ---------------------------------------------- | ----------- |
| 0:00–0:20 | Welcome, audience poll, the three scenarios    | —           |
| 0:20–0:50 | Lab 1: run the demo chain end to end           | Lab 1       |
| 0:50–1:20 | Lab 2: read the report like an analyst         | Lab 2       |
| 1:20–1:30 | Break                                          | —           |
| 1:30–2:10 | Lab 3: verify a seal by hand                   | Lab 3       |
| 2:10–2:45 | Lab 4: score interpretation — argue with it    | Lab 4       |
| 2:45–2:55 | Break                                          | —           |
| 2:55–3:30 | Labs 5–6: real chain + headless over SSH (VM)  | Labs 5–6    |
| 3:30–3:50 | Lab 7: evidence hand-off and teardown          | Lab 7       |
| 3:50–4:00 | Capstone briefing and close                    | Lab 8 brief |

## Setup (before the clock starts)

Every participant, from zero:

```bash
git clone https://github.com/karim852/Kage-DFIR-toolkit.git
cd Kage-DFIR-toolkit
python3 -m pip install -r requirements.txt
python3 preflight.py
python3 -m dfirconsole --demo
```

Open `http://127.0.0.1:8787`. If `preflight.py` reports missing pieces, that is expected on
macOS/Linux (the full chain is Windows-first) — demo mode is the classroom environment. The
Windows VMs are for labs 5–7 only.

## Lab 1 — Run the demo chain end to end (30 min)

**Goal:** see the whole workflow once, from an empty console to a finished report.

1. In Settings, tick **Demonstration mode**, set the **case reference** to
   `workshop-<yourname>`, set the analyst name, and save.
2. Untick nothing else. Click **Run the chain**.
3. While it runs, watch three things and note them down:
   - which step takes the longest (expected: the simulated collection, mirroring reality),
   - the moment each step prints its SEALED line,
   - what the live log says during `analyse`.
4. When the run finishes, write down the verdict line exactly:
   `80 / 100 · CONFIDENCE HIGH · 441 events analysed`.
5. **Checkpoint:** every participant has a finished chain with eleven sealed steps and can
   read the verdict aloud.

**Discussion (5 min):** why is the score identical on every machine? (Deterministic seed and
the same scenario; the demo is a fixture, not a screenshot.)

## Lab 2 — Read the report like an analyst (30 min)

**Goal:** turn a verdict into a one-paragraph finding.

1. Open the HTML report. Read the summary, then the four-axis breakdown, then the alert
   families, then the indicator list.
2. Answer these questions in writing:
   - Which axis contributes most to the 80? Which contributes least, and why?
   - What does the confidence line say, and which sources back it?
   - Name two findings you would escalate, and one you would not.
3. Write the one-paragraph triage summary a client could read: what happened, how sure we are,
   what to do next.
4. **Checkpoint:** each participant reads their paragraph aloud (30 seconds each); the room
   votes on the most defensible wording.

**Instructor note:** this is where "score is a triage priority, not proof" lands. Push back on
any paragraph that says "confirmed" when confidence is not high.

## Lab 3 — Verify a seal by hand (40 min)

**Goal:** prove to yourself that the seals are real.

1. In the chain panel, find a step whose seal basis is `content of <file>` — for example
   `content of hayabusa-output.csv`. Write down the 16-character seal.
2. Hash the file yourself:

   ```bash
   shasum -a 256 output/hayabusa-output.csv    # macOS
   sha256sum output/hayabusa-output.csv        # Linux
   ```

3. Compare the first 16 hex characters with the seal. They must match.
4. Now change the file (any byte) and hash again — the seal no longer matches. Restore the
   file, or press **Reset** and rerun the demo.
5. Find a step whose basis is `execution record (no file produced)` and explain in one
   sentence what that seal does and does not prove.
6. **Checkpoint:** every participant shows a matching hash, and a one-sentence limit statement
   for the fallback seal.

**Instructor note:** the honest limit — a seal proves non-alteration since sealing, not
collection completeness. That is `docs/trade-offs.md` material.

## Lab 4 — Score interpretation: argue with it (35 min)

**Goal:** treat the score as an argument, not an oracle.

1. Open the breakdown and write down the four components with their caps (45/25/20/10).
2. In pairs, answer:
   - Why can one noisy rule firing 300 times not reach a multi-stage verdict by volume alone?
     (Find the log-compression in `docs/explainers/scoring.md`.)
   - Which axis is zero in this run, and what would change it?
   - What would make confidence drop from `high` to `medium`? (Fewer independent sources, or
     logging coverage below 45%.)
3. Rewrite the verdict line for the same 80/100 as if confidence were `low`. Compare with the
   tool's wording: "Compromise highly likely — corroboration still limited".
4. **Checkpoint:** each pair submits one sentence answering "what would change my mind about
   this verdict?"

## Lab 5 — The real chain on a Windows VM (20 min)

**Goal:** run the chain against a host (a trainer-owned VM with a pre-seeded synthetic
intrusion — never a real machine).

1. SSH into your assigned VM; confirm Python and the tool are present at `C:\Kage`.
2. Start the console normally (visible) and run the chain with demo mode **off**. Because the
   VM's intrusion is synthetic and pre-installed, the collection is real but the content is
   safe by construction.
3. Watch step 04 (collection) dominate the wall-clock time — this is the real-world pacing.
4. **Checkpoint:** the chain completes and the report's seals cover real files on the VM.

**Safety gate (instructor):** these VMs exist solely for the workshop. No participant points
the tool at any machine they do not own or lack written authorization for.

## Lab 6 — Headless over SSH (15 min)

**Goal:** drive the VM's console from your own laptop without a window on the VM.

1. On the VM, create and run the scheduled task:

   ```cmd
   schtasks /Create /TN KageHeadless /TR "cmd /c cd /d C:\Kage && python -m dfirconsole" /SC ONCE /ST 23:59 /RL HIGHEST /F
   schtasks /Run /TN KageHeadless
   ```

2. Verify on the VM: `curl.exe -s -o NUL -w "%{http_code}" http://127.0.0.1:8787` → `200`.
3. From your laptop, open the tunnel:

   ```bash
   ssh -N -o ServerAliveInterval=30 -o ExitOnForwardFailure=yes -L 8787:127.0.0.1:8787 user@vm
   ```

   Open `http://127.0.0.1:8787` locally.
4. Start a chain, then close the browser tab mid-run; reopen it and confirm the run continues
   — the server kept running.
5. **Checkpoint:** every participant starts a run through the tunnel and survives a tab close.

## Lab 7 — Evidence hand-off and teardown (20 min)

**Goal:** end the engagement cleanly.

1. Copy the results deliberately over the same SSH channel:

   ```bash
   scp -r user@vm:C:/Kage/output ./kage-output
   ```

2. Verify one copied file's hash against the seal in the report — proof the copy is faithful.
3. Teardown on the VM:

   ```cmd
   schtasks /End /TN KageHeadless
   schtasks /Delete /TN KageHeadless /F
   ```

4. Two cleanups people forget: remove the Defender exclusion added by step 02
   (`Remove-MpPreference -ExclusionPath "C:\Kage"`, elevated), and remove the workshop SSH key
   if it was one-time.
5. **Checkpoint:** a verified copy in the participant's folder, a stopped task, and both
   cleanups confirmed.

## Lab 8 — Capstone brief (take-home)

Run a fresh demo chain with a different case name, then write a one-page triage note:
verdict, confidence, the two strongest findings, the one axis you trust least, and the single
command you would run next on a real host. Bring it to the next session (or the paid course's
day 3 for assessment).

## Common failures and fixes

| Symptom                              | Likely cause and fix                                              |
| ------------------------------------ | ----------------------------------------------------------------- |
| `preflight.py` warns about admin     | Expected off-Windows; demo mode needs no admin                    |
| Port 8787 already in use             | Another console instance — close it, or run with `--port 8788`    |
| SSH asks for a password on the VM    | Key in the wrong authorized-keys file (standard vs administrators) |
| Tunnel opens but page is blank       | Wrong port forwarded, or the task is not running — check `netstat` |
| Seal does not match after copying    | File changed in transit or the wrong file hashed — recheck both    |

## Instructor preparation

1. Build the VM images a day ahead; seed the synthetic intrusion and verify one full run.
2. Pre-create the SSH keys and place them per the standard/administrator split in
   `docs/headless-ssh.md`; test the tunnel from a participant-like machine.
3. Print or share this run sheet; every lab's checkpoint is the pacing device — do not let the
   room skip them.
4. Keep the two hard rules on the whiteboard for the whole session: **loopback only** and
   **authorized hosts only**.
