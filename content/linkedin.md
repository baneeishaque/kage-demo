# LinkedIn posts

Two posts, published 2–3 days apart. The first announces; the second teaches. Plain text —
LinkedIn rewards text posts over link posts, so put the link in the first comment.

## Post 1 — announcement (publish day 1)

> Most "DFIR triage" is a shoebox of tools and a prayer.
>
> One tool collects, another builds the timeline, a third scans, a fourth enriches — and
> nothing proves the evidence stayed untouched along the way.
>
> I put together an independent field guide + content kit for Kage, an open-source (MIT)
> Windows triage console that runs the whole chain as one sealed sequence:
>
> - 11 steps in one browser tab: collect → system context → Sigma timeline → analyse → YARA →
>   enrich → summary
> - Every completed step sealed with a SHA-256 that states what it covers ("content of
>   hayabusa-output.csv" vs "execution record")
> - A score on four capped axes — severity, kill-chain breadth, indicator reputation,
>   corroboration — with confidence kept separate from severity
> - A demo mode that runs the entire chain against a synthetic intrusion (80/100, 441 events)
>   without touching the machine
> - A headless-over-SSH workflow: elevated on the suspect host via a scheduled task, reached
>   through an SSH tunnel, evidence never leaving the disk
> - 115 tests, version 1.5.0
>
> The guide documents every workflow with exact commands — including the Defender exclusion
> people forget to remove afterwards.
>
> Tool by k0r1m. Guide + kit in the comments.
>
> #DFIR #IncidentResponse #Cybersecurity #Forensics #BlueTeam

**First comment:** `Guide + kit: https://github.com/baneeishaque-ompventure/kage-demo?utm_source=linkedin&utm_medium=social&utm_campaign=kage-launch`

## Post 2 — technical deep dive (day 3–4)

> Two details from a DFIR tooling deep dive that I keep thinking about:
>
> **1. The seal says what it covers.**
> A SHA-256 over a step is only meaningful if you know its basis. "seal 31d1991a65598de8 —
> content of hayabusa-output.csv" is verifiable. "seal 31d1991a65598de8 — execution record
> (no file produced)" is honest. Same mechanism, two very different claims — and the report
> states which one you got.
>
> **2. Elevation without a UAC prompt.**
> An SSH session on Windows can't prompt for UAC, but collection and the Defender exclusion
> need admin. The clean fix: a scheduled task with /RL HIGHEST — elevated, hidden, no prompt,
> and it survives your disconnect. Not a hack; documented Task Scheduler behavior.
>
> Both are written up with exact commands in the guide — including the full headless
> workflow, teardown, and the tunnel rule that keeps the console off the network.
>
> #DFIR #IncidentResponse #Windows #BlueTeam #Infosec

**First comment:** `Full write-up: https://github.com/baneeishaque-ompventure/kage-demo/blob/main/docs/headless-ssh.md?utm_source=linkedin&utm_medium=social&utm_campaign=kage-launch`

## Posting notes

- Post 1 between 08:00–10:00 on a Tuesday–Thursday; Post 2 the same slot 2–3 days later.
- Attach one image to each: Post 1 → `shot-seals.png` (the seal lines are the visual hook);
  Post 2 → `shot-terminal.png` (the `--demo` launch) or the chain panel mid-run.
- Do not edit the post in the first hour — edits suppress reach.
- Reply to every comment in the first two hours; that is where the distribution comes from.
