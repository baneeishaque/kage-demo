# Paid training — Kage Professional: Headless DFIR Triage (3 days)

A three-day, instructor-led professional course with a full capstone triage and an assessment.
It takes working engineers from the fundamentals through operating a host headlessly to
defending a verdict in a report review. This is the deepest format in the training set; the
one-day workshop is its compressed sibling.

## At a glance

| Item            | Value                                                             |
| --------------- | ----------------------------------------------------------------- |
| Length          | 3 days (≈7 hours/day incl. labs and breaks)                       |
| Audience        | Software/IT engineers, 2+ years experience; no DFIR background    |
| Class size      | 6–16 participants                                                 |
| Delivery        | In person or virtual with breakouts; labs on trainer-owned VMs    |
| Prerequisites   | Laptop with Python 3.10+, SSH client; the free course as pre-work |
| Certification   | Capstone triage + written assessment, graded against a rubric     |

## Pre-work (1 week before)

1. Complete the free course (or prove equivalent comfort): run a demo chain, verify a seal,
   and write a one-paragraph triage note.
2. Read `docs/architecture.md` and `docs/trade-offs.md`.
3. Verify SSH access to your assigned lab VM; report any credential issues before day 1.
4. Bring a written one-sentence answer to: "what does a seal prove?" — it will be collected,
   not graded, on day 1.

## Day 1 — Foundations and the chain

**Goal:** understand every step and run the whole chain with intent.

| Time      | Segment                                                        |
| --------- | -------------------------------------------------------------- |
| 0:00–0:45 | Introductions, the three scenarios, ground rules, safety gate  |
| 0:45–1:30 | Triage and chain of custody: the concepts that decide disputes |
| 1:30–2:15 | Architecture deep dive: the eleven steps, in code and on screen |
| 2:15–2:30 | Break                                                          |
| 2:30–3:30 | Lab 1: install, configure, run the demo chain end to end        |
| 3:30–4:30 | Lab 2: step-by-step teardown — what each step produced, in what order |
| 4:30–4:45 | Break                                                          |
| 4:45–5:45 | Lab 3: the real chain on the lab VM (synthetic intrusion)       |
| 5:45–6:30 | Debrief: what differed between demo and real collection, and why |
| 6:30–7:00 | Day 1 quiz and open Q&A                                        |

**Day 1 labs, step by step:**

1. Clone, install, preflight, launch `--demo`; tour the console (30 min).
2. Run the chain; annotate each step's output and required/optional status (60 min).
3. On the VM: run with demo mode off; time step 04; note the real file sizes (60 min).
4. Compare the two runs: which numbers changed, which stayed fixed, and why (30 min).

**Day 1 checkpoint:** every participant has two finished chains (demo and VM) and an
eleven-row annotation table.

## Day 2 — Custody, scoring, and reporting

**Goal:** prove integrity, interpret scores, and write reports that survive review.

| Time      | Segment                                                        |
| --------- | -------------------------------------------------------------- |
| 0:00–0:45 | Seals: the contract, the basis strings, the limits              |
| 0:45–1:30 | Lab 4: verify seals by hand; break a file; document the mismatch |
| 1:30–2:15 | Scoring deep dive: caps, compression, corroboration             |
| 2:15–2:30 | Break                                                          |
| 2:30–3:30 | Lab 5: recompute a breakdown by hand; predict score changes     |
| 3:30–4:15 | Confidence and verdict wording: evidence quality vs severity    |
| 4:15–4:30 | Break                                                          |
| 4:30–5:30 | Lab 6: write the client-ready report for the VM run             |
| 5:30–6:30 | Report review: swap reports, red-team each other's claims       |
| 6:30–7:00 | Day 2 quiz and open Q&A                                        |

**Day 2 labs, step by step:**

1. Hash every sealed file on the VM and compare with the report (45 min).
2. Take one demo breakdown; recompute each axis by hand, then modify one input (double the
   noisy alerts; drop one source) and predict the new score before rerunning (60 min).
3. Write a two-page report: verdict, confidence, findings, indicators, limits, next actions
   (60 min).
4. Red-team pass: find one overclaim, one missing limit, one unverifiable statement in your
   partner's report (60 min).

**Day 2 checkpoint:** a hand-verified seal chain, a correctly predicted score change, and a
red-teamed report.

## Day 3 — Headless operations and capstone

**Goal:** operate a host without disrupting it, then run and defend a full triage.

| Time      | Segment                                                        |
| --------- | -------------------------------------------------------------- |
| 0:00–0:45 | Headless patterns: scheduled tasks, tunnels, disconnection survival |
| 0:45–1:45 | Lab 7: end-to-end headless run on the VM through a tunnel       |
| 1:45–2:00 | Break                                                          |
| 2:00–2:45 | Failure drills: port conflicts, stale tasks, key misplacement   |
| 2:45–3:30 | Evidence hand-off: copy, verify, teardown, the two forgotten cleanups |
| 3:30–3:45 | Break                                                          |
| 3:45–4:15 | Capstone briefing: the scenario, the constraints, the clock     |
| 4:15–6:15 | Capstone: full triage under time pressure (demo + VM phases)    |
| 6:15–6:45 | Capstone defence: each participant presents their verdict       |
| 6:45–7:00 | Assessment results, certification, next steps                   |

**Capstone scenario (instructor-seeded VM):** a workstation with a synthetic multi-stage
intrusion. Constraints, stated up front: the owner is working, so the console must be started
headless; one required cleanup will be checked (the Defender exclusion); the report must state
its limits honestly. Deliverables: the sealed chain, the report, the verified evidence copy,
and a five-minute defence of the verdict.

**Assessment rubric (100 points):**

| Criterion                          | Points | What earns full marks                                  |
| ---------------------------------- | ------ | ------------------------------------------------------ |
| Chain executed and sealed          | 20     | All required steps sealed; failures explained          |
| Seal verification                  | 20     | Hashes match; limits stated                            |
| Score interpretation               | 20     | Axes explained; no overclaiming; confidence respected  |
| Report quality                     | 20     | Client-readable; findings, limits, next actions clear  |
| Headless discipline and teardown   | 20     | Loopback only; task deleted; exclusion removed; key gone |

Pass = 70 points, with no criterion below 10.

## Delivery notes for the instructor

- Build fresh VM images per cohort; seed the intrusion, then verify one full run per image.
- Run the failure drills live — deliberately misplace a key and watch the room debug it; that
  table in `docs/headless-ssh.md` is the answer key.
- Keep the two hard rules visible all three days: **loopback only**, **authorized hosts only**.
- Record the capstone defences (with consent) — they are the best marketing for the next
  cohort, and the best Q&A bank for the webinar.

## Pricing and logistics (fill per cohort)

| Item                          | Value                                   |
| ----------------------------- | --------------------------------------- |
| Seat price                    | `TBD`                                   |
| Group discount (4+)           | `TBD`                                   |
| Private cohort (on-site)      | `TBD`                                   |
| Included                      | Lab VM access, materials, certification |
| Cancellation                  | `TBD`                                   |

## After the course

- Certificate and assessment breakdown sent within one week.
- Alumni thread for capstone questions, kept open for 90 days.
- Suggested next steps: contribute a scenario or explainer upstream to the guide repository,
  or deliver the free webinar to your own community.
