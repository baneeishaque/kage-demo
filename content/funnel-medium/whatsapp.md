# WhatsApp — DFIR and Windows-admin groups

Messages for security / DFIR / Windows-administrator community groups. Share **once per
group**, ideally after asking an admin. The goal is a useful artifact, not a broadcast; the
etiquette notes below are part of the deliverable.

## Short version (default)

> Hi all — I wrote up a DFIR triage workflow built around a constraint I kept running into:
> examine a Windows host without disturbing the person using it.
>
> The interesting parts aren't the tools — it's the custody: every completed step is sealed
> with a SHA-256 that states what it covers, and the score is four capped axes you can audit
> instead of one opaque number.
>
> Full article: <https://medium.com/@baneeishaque/kage-headless-dfir-triage?utm_source=whatsapp&utm_medium=social&utm_campaign=kage-headless-medium>
>
> The guide + kit (MIT) is on GitHub if you want the headless-over-SSH procedure and the
> line-by-line explainers. Happy to answer questions here.

## Longer version (groups that welcome detail)

> Sharing something I wish I'd found before my first on-the-fly triage.
>
> It's an independent guide to Kage, an open-source (MIT) Windows DFIR triage console. The
> parts worth reading:
>
> • one chain of 11 steps in one browser tab — CyLR collection, Hayabusa/Sigma timeline, THOR
> Lite, enrichment, summary — no tool sprawl
> • every completed step sealed with a SHA-256, and the seal states its basis: "content of
> hayabusa-output.csv" or "execution record (no file produced)"
> • a score of four capped axes (severity ≤45 log-compressed, kill chain ≤25, reputation ≤20,
> corroboration ≤10) published with its breakdown, plus confidence as a separate statement
> • headless over SSH: Task Scheduler with /RL HIGHEST starts it hidden and elevated on the
> host; one SSH tunnel brings the UI to your machine; the evidence never moves
> • a deterministic demo mode — 80/100, confidence high, 441 events analysed — that reads,
> scans, and changes nothing
>
> Full write-up, including the trade-offs (loopback-only, no authentication by design,
> Windows-first):
> <https://medium.com/@baneeishaque/kage-headless-dfir-triage?utm_source=whatsapp&utm_medium=social&utm_campaign=kage-headless-medium>
>
> Guide + kit (MIT): github.com/baneeishaque-ompventure/kage-demo
>
> Point it only at hosts you own or are explicitly authorized to examine — written scope
> first, always.

## Etiquette

- Ask an admin before posting in moderated groups; "sharing a write-up I made" gets a warmer
  reception than a silent drop.
- Share once per group. Do not repost when engagement is low; the article is evergreen.
- Answer questions in the group instead of DMing links.
- If the group has a "share your projects" day or thread, use it — and drop the longer version
  there.
- Never paste seals, hashes, or artifacts from a real case into a group; the demo output is
  the only safe material to quote.
