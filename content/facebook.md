# Facebook post

For a developer-focused Page or a security/DFIR group. One post, link in the body is fine on
Facebook (unlike LinkedIn).

***

Most "DFIR triage" is a shoebox of tools and a prayer — one tool collects, another builds the
timeline, a third scans, and nothing proves the evidence stayed untouched along the way.

I wrote up an independent field guide for Kage, an open-source (MIT) Windows triage console
that runs the whole chain as one sealed sequence:

- 11 steps in one browser tab, streamed live: collect → system context → Sigma timeline →
  analyse → YARA → enrich → summary
- Every completed step sealed with a SHA-256 that states what it covers — the content of a
  named file, or the execution record alone
- A score on four capped axes (severity, kill-chain breadth, indicator reputation,
  corroboration) with confidence kept separate from severity
- A demo mode that runs the entire chain against a synthetic intrusion (80/100, 441 events)
  without reading, scanning, or changing anything
- A headless-over-SSH workflow: elevated on the suspect host via a scheduled task, reached
  through an SSH tunnel, evidence never leaving the disk
- 115 tests, version 1.5.0

The guide includes exact commands, teardown (don't forget the Defender exclusion), and a
troubleshooting table. Tool by k0r1m.

Guide + kit:
<https://github.com/baneeishaque-ompventure/kage-demo?utm_source=facebook&utm_medium=social&utm_campaign=kage-launch>

Feedback welcome — especially from anyone who has run triage on a machine they couldn't sit
in front of.

***

## Posting notes

- Attach the `shot-seals.png` / `shot-chain.png` pair as a two-image post; the seal block is
  the visual that stops the scroll.
- Post in the 12:00–14:00 window; security groups are most active around lunch.
- For groups: check self-promotion rules first; if link posts are restricted, put the link in
  the first comment and keep the body as above minus the URL line.
- Keep the "authorized hosts only" spirit: if a commenter asks "can I run this on my
  company's fleet?", the answer is: written permission first, one host per run.
