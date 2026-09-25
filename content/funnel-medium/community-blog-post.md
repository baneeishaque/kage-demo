# Community blog post

Companion post for community platforms (dev.to, Hashnode, a company blog, or a community
newsletter). Warmer and shorter than the Medium deep dive; it tells the story and hands the
reader off to the article.

***

## The laptop I wasn't allowed to disturb

The request arrived on a Tuesday morning: a workstation needed a first-pass triage, and the
person who used it had a deadline. No imaging window, no spare machine, no "we'll take it
offline for a few hours". The evidence had to be collected while the owner kept working.

I had done this the hard way before — a folder of tools, a checklist in my head, and a
sinking feeling every time a step finished and nobody could prove what had happened to the
output between then and the report. This time I wanted the opposite: one chain, sealed as it
runs, driven from my own laptop while the machine stayed in use.

That workflow is the subject of the full write-up, and the tool that makes it possible is
**Kage** — an open-source, MIT-licensed Windows DFIR triage console. It is public, it has a
deterministic demo mode that touches nothing, and every claim below can be rehearsed on your
own machine before it ever meets real evidence.

Here is what building the guide taught me.

### 1. A seal that doesn't state its basis is decoration

The part of Kage I keep coming back to is small: every completed step is sealed with a
SHA-256, and the seal **says what it covers**:

```text
✔ Analyse the timeline              SEALED 0.1s
  seal 31d1991a65598de8 — content of hayabusa-output.csv
✔ Exclude the folder from Defender  SEALED 6.2s
  seal 8f2c04b71ae93d55 — execution record (no file produced)
```

The first seal proves a file's contents. The second proves an action happened — and says so
instead of implying more. That distinction is the difference between a report a client can
review and a number nobody can audit. If you build any collection pipeline, steal this rule
first: **make every claim state its basis.**

### 2. Cap your scoring axes, and keep confidence separate

"Threat score: 87" tells you nothing you can argue with. Kage's score is four independent
axes, each capped — severity (max 45, log-compressed as `6.5 × √raw`), kill-chain breadth
(25), indicator reputation (20, and only externally confirmed indicators count), and
corroboration between independent sources (10).

Two properties matter more than the total. **No single source can dominate** — a noisy rule
firing hundreds of times saturates its own axis and cannot reach a multi-stage verdict by
volume alone. And **confidence is a separate statement about evidence quality**: a high score
from the Sigma timeline alone reads as a strong lead, never a confirmation. The verdict
wording changes with it — "Compromise highly likely — corroboration still limited" is what an
80/100 with thin corroboration actually is.

### 3. Headless is a workflow, not a hack

The constraint — don't disturb the machine — turned out to have a clean answer. The console is
a local web app, so it needs no desktop session at all. On the host, a Task Scheduler task
with `/RL HIGHEST` starts it elevated, hidden, and survives your disconnect. On your machine,
one SSH tunnel brings the UI over:

```bash
ssh -N -o ServerAliveInterval=30 -o ExitOnForwardFailure=yes -L 8787:127.0.0.1:8787 user@host
```

The evidence never moves — collection, timeline, report, and seals all stay on the host's
disk; only the live stream travels. The console ships no authentication by design, which is
exactly why the tunnel is the boundary and why you never bind it to `0.0.0.0`.

### 4. Rehearse on synthetic evidence first

Before any of this met a real machine, it ran against a fictional one. Demo mode executes the
entire eleven-step chain against a synthetic intrusion — a phishing chain to encoded
PowerShell, an EICAR hash, Defender disabled, an AMSI bypass, LSASS access, command-and-control
to a hard-coded address, shadow copies deleted, the log cleared — buried under 420 events of
ordinary Windows noise. Fixed seed, same story every run, nothing on your machine read,
scanned, or changed.

A full demo run lands at **80/100 with confidence high — 441 events analysed**. That is how
you learn where the buttons are, how you smoke-test a deployment, and how you produce
screenshots and recordings without a single evidence-handling concern.

### The meta-proof

The best part of this project is that the guide describes itself. Its repository includes a
scripted video pipeline that narrates the walkthrough in **20 languages** and ships subtitles
in **37** — English, Hindi, Malayalam, Tamil, Kannada, Bengali, Arabic, and more. It was
produced entirely from demo mode: the system the video describes is the system that produced
the video, with no real evidence involved.

### What to do with this

The full write-up — the chain architecture, the seal implementation, the scoring model, the
headless procedure, and every trade-off — is on Medium:

> [The host you must not disturb: headless DFIR triage, sealed step by step](https://medium.com/@baneeishaque/kage-headless-dfir-triage?utm_source=community-blog&utm_medium=referral)

And the guide, with the reasoning written next to it, is here:

**Guide + kit: [github.com/baneeishaque-ompventure/kage-demo](https://github.com/baneeishaque-ompventure/kage-demo)**

If you only take one thing: pick the property you cannot prove about your own triage pipeline
today, and make it provable — a seal, a capped axis, a test. The tools are the easy part.
Custody is what survives the report review.

***

Kage is created by k0r1m and released under the MIT license. This write-up is an independent
guide, not affiliated with or endorsed by the author. Point it only at hosts you own or are
explicitly authorized to examine.
