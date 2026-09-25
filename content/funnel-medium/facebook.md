# Facebook post

One post for a developer or security group (or your page). Conversational, no jargon walls.
The link goes in the first comment (Facebook also suppresses link posts).

***

**Post text:**

I got a triage request once that came with a constraint: examine the workstation, don't
disturb the person using it. No imaging window, no spare machine, and the report still had to
prove the evidence was never touched.

So I spent time with a tool built around exactly that: Kage, an open-source Windows DFIR
triage console. The parts I keep telling people about:

- One chain of 11 steps in a single browser tab — collection, timeline, YARA scan,
  enrichment, summary — instead of five tools glued together by the analyst.
- Every completed step sealed with a SHA-256, and the seal states what it covers: the content
  of a file, or the execution record when no file was produced. No vague hashes.
- A score made of four capped axes, published with its breakdown — and confidence as a
  separate line, because "how sure are we" is not the same question as "how bad is it".
- Headless over SSH: started hidden and elevated on the host, driven from your own machine,
  evidence never leaving the host's disk.

It also ships a demo mode that runs the whole chain against a synthetic intrusion — 80/100,
confidence high, 441 events analysed — so you can rehearse the workflow without touching a
real machine. Nothing read, scanned, or changed.

I wrote the whole story — the architecture, the seal implementation, the scoring model, the
headless procedure, and the trade-offs — as an article. It's a good read if you've ever had
to triage a machine someone was still using.

Link in the first comment.

**First comment:** `The host you must not disturb: https://medium.com/@baneeishaque/kage-headless-dfir-triage?utm_source=facebook&utm_medium=social&utm_campaign=kage-headless-medium`

## Posting notes

- Post in DFIR / blue-team / Windows-admin groups only where self-promotion is allowed; read
  the rules first.
- One image: the sealed chain panel (`shot-seals.png`) or the score breakdown
  (`shot-score.png`).
- Reply to questions with specifics — "how do you verify a seal later?" is the conversation to
  lean into.
- Keep the authorization note in replies: point it only at hosts you own or are explicitly
  authorized to examine.
