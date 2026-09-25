# X / Twitter thread

10 posts. Post 1 is the hook; the link lands in the final post (keep it out of the opener so
the algorithm does not down-rank the thread). Keep every post under 280 characters.

***

**1/10**
The hardest DFIR request: examine a workstation, don't disturb the person using it.

No imaging window. No spare machine. And the report had to prove the evidence was never
touched.

That constraint became a workflow. Thread:

**2/10**
Most "DFIR triage" is a shoebox of tools glued together by the analyst.

One collects. Another builds the timeline. A third scans. Nothing proves what happened to the
output between steps.

The fix: one chain, 11 steps, one browser tab.

**3/10**
Every completed step gets a SHA-256 seal — and the seal states its basis:

"seal 31d1991a65598de8 — content of hayabusa-output.csv"

or, when the step produced no file:

"seal 8f2c04b71ae93d55 — execution record (no file produced)"

A claim you can audit, or one that says it can't be.

**4/10**
The score is four capped axes, not an opaque number:

• severity ≤ 45 (log-compressed: 6.5 × √raw)
• kill chain ≤ 25 (decisive tactics weighted)
• reputation ≤ 20 (externally confirmed only)
• corroboration ≤ 10 (independent sources agreeing)

No single noisy source can fake a multi-stage verdict.

**5/10**
Confidence is separate from severity.

It counts independent sources agreeing — and "high" also requires the host's log coverage to
be at least 45%.

A verdict is only worth what the machine agreed to record.

**6/10**
Headless without disrupting work:

The console is a local web app. No desktop session needed.

Task Scheduler + /RL HIGHEST → elevated, hidden, survives disconnect.

One SSH tunnel → the UI on your machine. Evidence stays on the host's disk.

**7/10**
The console ships no authentication — by design.

The SSH tunnel is the security boundary. Never bind it to 0.0.0.0.

Loopback-only is the feature. Say it in the docs, and in the file's docstring.

**8/10**
Before it meets a real machine, rehearse on a fake one.

Demo mode runs the whole chain against a synthetic intrusion under 420 noise events. Fixed
seed, same story every run.

80/100 · confidence high · 441 events analysed. Nothing read, scanned, or changed.

**9/10**
The meta-proof:

The guide's own video pipeline narrates the walkthrough in 20 languages and subtitles it in
37 — produced entirely from demo mode.

The system the video describes is the system that produced the video.

**10/10**
Full write-up — chain design, seal implementation, scoring model, headless procedure, and the
trade-offs:

<https://medium.com/@baneeishaque/kage-headless-dfir-triage?utm_source=x&utm_medium=social&utm_campaign=kage-headless-medium>

Guide + kit (MIT): github.com/baneeishaque-ompventure/kage-demo

A repost helps if this is useful.

***

## Posting notes

- Post the thread 2–3 hours after the LinkedIn post goes live; do not run them simultaneously.
- Attach `shot-seals.png` to post 3 and `shot-score.png` to post 4 — the two artifacts the
  thread is actually about.
- Quote-post your own post 1 the next morning with the article link for a second wave.
