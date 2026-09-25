# Training programs

Four ready-to-deliver training formats for teaching **headless DFIR triage with Kage** — a
free community webinar, a self-paced free course, a hands-on workshop, and a paid professional
course. They share one audience, one lab environment, and one narrative spine; they differ in
depth, format, and commitment.

Everything here is independent training material for
[Kage](https://github.com/karim852/Kage-DFIR-toolkit) (MIT), built on the guide in this
repository: [`docs/`](../../docs/README.md), the
[`content/funnel-medium/`](../funnel-medium/README.md) article, and the
[37-locale video](../youtube/README.md). Demo mode means every exercise runs on synthetic
evidence — no real hosts, no authorization paperwork, no risk.

## Program map

| Program                             | Format              | Length     | Audience           | Cost | Commitment |
| ----------------------------------- | ------------------- | ---------- | ------------------ | ---- | ---------- |
| [`webinar.md`](webinar.md)          | Live online session | 90 min     | Anyone curious     | Free | 1 sitting  |
| [`free-training.md`](free-training.md) | Self-paced course | 6–8 hours  | Beginners          | Free | 1–2 weeks  |
| [`hands-on-session.md`](hands-on-session.md) | Instructor-led workshop | 4 hours | Practitioners | Paid seat | 1 day |
| [`paid-training.md`](paid-training.md) | Professional course | 3 days    | Working engineers  | Paid | 3 days + capstone |

**The ladder**: the webinar creates interest → the free course builds fundamentals → the
hands-on session applies them to a live host → the professional course certifies the skill
with a full triage capstone. Each program stands alone; none requires the one before it.

## Audience

Software developers and IT engineers with roughly **two years of experience** — comfortable
with a terminal, basic networking (SSH, ports, tunnels), and reading code in any language.
**No DFIR background is assumed.** The programs start at "what is triage?" and end at
"defend your verdict in a report review."

Difficulty rises deliberately within and across programs: read commands → run a chain →
interpret a score → verify a seal by hand → operate a host headlessly → run and defend a full
triage.

## Shared environment

| Need                    | Webinar | Free course | Workshop | Pro course |
| ----------------------- | ------- | ----------- | -------- | ---------- |
| Laptop (macOS/Linux/Win)| yes     | yes         | yes      | yes        |
| Python 3.10+            | yes     | yes         | yes      | yes        |
| Browser                 | yes     | yes         | yes      | yes        |
| Windows VM              | no      | optional    | yes (for labs 5–7) | yes |
| SSH client              | no      | optional    | yes      | yes        |

Setup for every program, from zero:

```bash
git clone https://github.com/karim852/Kage-DFIR-toolkit.git
cd Kage-DFIR-toolkit
python3 -m pip install -r requirements.txt
python3 -m dfirconsole --demo
```

Then open `http://127.0.0.1:8787`. On macOS and Linux demo mode is the default; the checkbox
in Settings forces it anywhere.

## Real-life examples used throughout

Every program returns to the same three scenarios, drawn from the guide:

1. **The finance laptop** — triage needed today, the owner has a deadline, no imaging window.
   (The headless-over-SSH case.)
2. **The noisy alert** — one Sigma rule fires 300 times. (The capped-axis case: volume must
   not fake a verdict.)
3. **The report review** — a client asks "how do you know the evidence wasn't altered?"
   (The seal-basis case.)

## Instructor prerequisites

- Run the full demo chain once and read `docs/architecture.md`, `docs/trade-offs.md`, and
  `docs/headless-ssh.md` before teaching any format.
- Rehearse the live demo; keep the exact commands from each program's run sheet in a terminal
  history or a scratch file.
- Know the two hard rules by heart: **loopback only, never `--host 0.0.0.0`**, and
  **authorized hosts only, written scope first**.
- Every demo uses demo mode; never show real evidence, real hostnames, or case data.

## Safety and authorization (all programs)

- Demo mode touches nothing — use it for all classroom work by default.
- Real-host labs (workshop labs 5–7, pro-course capstone) run on **purpose-built VMs** owned
  by the trainer, with the synthetic intrusion pre-seeded.
- If a participant wants to triage a real host, the guide's rule applies: written
  authorization, agreed scope, documented hand-off — before any command runs.

## How the programs compose with this repository

| Artifact                                   | Used by                                  |
| ------------------------------------------ | ---------------------------------------- |
| `docs/architecture.md`                     | All — the chain, score, and seal reference |
| `docs/trade-offs.md`                       | Workshop, pro course — the "argue with it" material |
| `docs/headless-ssh.md`                     | Webinar (demo), workshop, pro course     |
| `docs/explainers/`                         | Free course, pro course — line-by-line code |
| `content/funnel-medium/medium-article.md`  | Post-webinar and post-course reading     |
| `content/youtube/` (37-locale video)       | Pre-work for every program; used live in the webinar |
