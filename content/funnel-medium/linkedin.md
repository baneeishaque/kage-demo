# LinkedIn posts

Two posts, 2–3 days apart, both funnelling to the Medium article. Plain text with the link in
the first comment — LinkedIn rewards text posts over link posts.

## Post 1 — the hook (day 1)

> The request that taught me the most about DFIR triage: examine a workstation, and don't
> disturb the person using it.
>
> No imaging window. No spare machine. The evidence had to be collected while the owner kept
> working — and the report had to prove the evidence was never touched.
>
> The console I write about does three things differently:
>
> • one chain of 11 steps in one browser tab — collection, timeline, YARA, enrichment, summary
> — instead of five tools glued together by the analyst
> • every completed step sealed with a SHA-256, and the seal states what it covers: the
> content of a file, or the execution record when no file was produced
> • headless over SSH — started hidden and elevated on the host, driven from my own machine,
> evidence never leaving the host's disk
>
> It also has a deterministic demo mode that runs the whole chain against a synthetic
> intrusion — 80/100, confidence high, 441 events analysed — so you can rehearse the workflow
> before it meets real evidence.
>
> The full system design, including the trade-offs I documented instead of hiding, is in the
> article below.
>
> #DFIR #IncidentResponse #CyberSecurity #Windows #DigitalForensics

**First comment:** `The host you must not disturb — full write-up: https://medium.com/@baneeishaque/kage-headless-dfir-triage?utm_source=linkedin&utm_medium=social&utm_campaign=kage-headless-medium`

## Post 2 — the engineering story (day 3–4)

> The most useful design rule I learned from DFIR triage tooling:
>
> Make every claim state its basis.
>
> When a step finishes, "seal 31d1991a65598de8" is decoration. "seal 31d1991a65598de8 — content
> of hayabusa-output.csv" is a statement you can audit — and when a step produced no file, the
> seal says "execution record (no file produced)" instead of implying more than it can prove.
>
> Three more decisions that paid off:
>
> 1. Cap every scoring axis. Severity is log-compressed (6.5 × √raw, capped at 45), kill-chain
>    breadth is capped at 25, reputation at 20 (externally confirmed indicators only), and
>    corroboration at 10. No single noisy source can fake a multi-stage verdict.
>
> 2. Keep confidence separate from severity. It counts independent sources agreeing — and
>    "high" additionally requires the host's log coverage to be at least 45%, because a
>    verdict is only worth what the machine agreed to record.
>
> 3. Run it headless. A local web app needs no desktop session; a Task Scheduler task with
>    /RL HIGHEST elevates without a UAC prompt, and an SSH tunnel brings the UI to your
>    machine while the evidence stays on the host.
>
> The article breaks down the full architecture, the seal implementation, and the trade-offs —
> including why the console deliberately ships with no authentication.
>
> #SoftwareEngineering #CyberSecurity #DFIR #IncidentResponse #BlueTeam

**First comment:** `Architecture + trade-offs: https://medium.com/@baneeishaque/kage-headless-dfir-triage?utm_source=linkedin&utm_medium=social&utm_campaign=kage-headless-medium`

## Posting notes

- Post 1 between 08:00–10:00 on a Tuesday–Thursday; Post 2 the same slot 2–3 days later.
- Attach one image per post: Post 1 → the sealed chain panel (`shot-seals.png`); Post 2 → the
  four-axis score breakdown (`shot-score.png`) — the auditable score is the whole point.
- Do not edit a post in the first hour; edits suppress reach.
- Reply to every comment in the first two hours — that is where the distribution comes from.
- Security audiences respond to specifics: "how did you verify the seals?" is the conversation
  to lean into, not the star count.
