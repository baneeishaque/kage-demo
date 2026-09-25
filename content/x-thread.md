# X / Twitter thread

12 posts. Post 1 is the hook; every post stands alone if quoted. Attach `shot-seals.png` to
post 3, `shot-overview.png` to post 5, and `shot-terminal.png` to post 7.

***

**1/**
Most "DFIR triage" is a folder of batch files and hope.

One tool collects. Another builds the timeline. A third scans. Nothing proves the evidence
stayed untouched.

I wrote up a console that runs the whole chain as 11 sealed steps — and how to drive it
headless over SSH.

Thread:

**2/**
The chain, in one browser tab:

01 Prepare workspace
02 Exclude from Defender
03 Locate tooling
04 Collect (CyLR)
05 System context
06 Update Sigma rules
07 Build timeline (Hayabusa)
08 Analyse
09 YARA scan (THOR Lite)
10 Enrich (VT + AbuseIPDB)
11 Write summary

Live-streamed. Server-side. Refresh-safe.

**3/**
The decision that makes it defensible: every completed step is sealed — and the seal states
what it covers.

✔ Analyse the timeline — SEALED
  seal 31d1991a65598de8 — content of hayabusa-output.csv
✔ Exclude from Defender — SEALED
  seal 8f2c04b71ae93d55 — execution record (no file produced)

Two different claims. Both explicit.

**4/**
The score is 4 capped axes, not one magic number:

- severity (max 45, log-compressed as 6.5 × √raw)
- kill-chain breadth (25)
- indicator reputation (20, externally confirmed only)
- corroboration (10)

No single source can dominate. And confidence is a *separate* statement from severity.

**5/**
Before touching a real host: demo mode.

It generates a synthetic intrusion — encoded PowerShell, LSASS access, C2 with a JA3 match,
shadow copies deleted — under 420 noise events.

Full run: 80/100, CONFIDENCE HIGH, 441 events analysed.

It reads nothing. Scans nothing. Changes nothing.

**6/**
The workflow that matters: headless on the suspect host.

Obstacle: SSH on Windows is non-interactive — UAC can't prompt, but collection needs admin.

The fix: a scheduled task with /RL HIGHEST. Elevated, hidden, no prompt, survives your
disconnect.

**7/**
The whole elevation:

schtasks /Create /TN KageHeadless /TR "cmd /c cd /d C:\Kage && python -m dfirconsole"
  /SC ONCE /ST 23:59 /RL HIGHEST /F
schtasks /Run /TN KageHeadless

Then from your machine:

ssh -N -o ServerAliveInterval=30 -L 8787:127.0.0.1:8787 user@host

**8/**
Why the tunnel is not optional:

The console binds to 127.0.0.1 and ships NO authentication. No tokens. No TLS.

That's a design decision. The SSH tunnel is the security boundary.

Never launch with --host 0.0.0.0. A network-reachable triage console is a remote-control
forensic tool.

**9/**
Evidence never moves.

Collection, timeline, report, and seals all stay on the host's disk. Only the live stream
travels over the tunnel.

You copy the results off deliberately — over the same SSH channel — and document the
hand-off.

**10/**
Underrated step: capture the present tense.

Event logs describe what happened. Step 05 captures what's happening — read-only: accounts,
listening ports matched to processes, disk-root anomalies, and the machine's logging
coverage.

A verdict is only worth what the host agreed to record.

**11/**
Honest limits:

- score is a triage heuristic, not proof
- Windows-first (Linux in progress)
- THOR Lite needs registration; without it the YARA step is skipped
- API keys optional; without them, a local deterministic report
- one host per run

**12/**
Full guide + kit (exact commands, teardown, troubleshooting):
<https://github.com/baneeishaque-ompventure/kage-demo?utm_source=x&utm_medium=social&utm_campaign=kage-launch>

The tool (MIT, by k0r1m):
<https://github.com/karim852/Kage-DFIR-toolkit>

Authorized hosts only. If you triage with it, tell me what surprised you.

## Posting notes

- Post the whole thread in one sitting; reply-to-self chaining only for the first 2 posts
  (later posts get more reach as standalone posts if the thread is quote-tweetable).
- Pin post 1 for a week.
- Quote-post post 3 (the seal block) after 48 hours — it is the most distinctive artifact.
