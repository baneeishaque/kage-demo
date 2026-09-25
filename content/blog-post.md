# One host, eleven sealed steps, zero tool sprawl: DFIR triage done properly

*Canonical article for the blog / dev.to / Hashnode. Syndicate to Medium with a canonical link
back to this URL.*

There is a moment every incident responder hits. A machine needs a first-pass triage — now —
and the response is a shoebox: one tool collects the artefacts, another builds a timeline, a
third scans for malware, a fourth enriches the indicators, and a fifth, if you are lucky,
writes something you can hand to a client. Every hop loses context. Nothing proves the
evidence stayed untouched. The analyst becomes the integration layer, at the exact moment
they should be thinking about the intrusion.

This article walks through a reference workflow that answers those questions up front: eleven
steps in one console, a hash-sealed chain of custody, a score you can argue with line by line,
and a rehearsal mode that proves the entire pipeline before it touches real evidence. It also
covers the part nobody writes down — running the console headless on the suspect machine
while you work from your own.

The tool is **Kage** (影, "shadow"), an open-source, MIT-licensed Windows DFIR triage console.
Everything below was verified against version 1.5.0 and a real demo run; the guide and the
publishing kit live here:

**Guide + kit: [github.com/baneeishaque-ompventure/kage-demo](https://github.com/baneeishaque-ompventure/kage-demo)**

## The decisions that matter (and the ones that don't)

Triage architecture is mostly five decisions. Everything else is execution.

**1. One chain, not ten tools.** Kage orchestrates the tools that already earned their
reputation — CyLR for collection, Hayabusa and its Sigma rules for the timeline, THOR Lite
for the YARA scan, VirusTotal and AbuseIPDB for enrichment — but the analyst drives one chain
in one browser tab. Every command streams live as it runs, and the analysis runs server-side:
refresh the page mid-collection and nothing is lost. The eleven steps are Prepare the
workspace, Exclude the folder from Defender, Locate the tooling, Collect the artefacts,
Capture the system context, Update the Sigma rules, Build the timeline, Analyse the timeline,
YARA scan (THOR Lite), Enrich the indicators, and Write the summary.

**2. Seal every step — and state what the seal covers.** This is the decision that separates a
script folder from something defensible. When a step completes, it is sealed with a SHA-256 —
and the seal declares its basis:

```text
✔ Analyse the timeline              SEALED 0.1s
  seal 31d1991a65598de8 — content of hayabusa-output.csv
✔ Exclude the folder from Defender  SEALED 6.2s
  seal 8f2c04b71ae93d55 — execution record (no file produced)
```

When a step produced files, the seal is the hash of their contents: rerun the step a week
later and you have proof the artefact was not altered. When it produced none, the seal covers
only the execution record — and says so, rather than implying more than it can prove.

**3. Score on capped axes, and keep confidence separate.** "Threat score: 87" is a number
nobody can audit. Kage's score is four independent axes, each capped: severity (max 45,
log-compressed as 6.5 × √raw alert volume), kill-chain breadth (25), indicator reputation
(20 — and only externally confirmed indicators count), and corroboration between independent
sources (10). Two properties matter more than the total: no single source can dominate — a
noisy rule firing hundreds of times saturates its own axis and cannot reach a multi-stage
verdict by volume alone — and confidence is a separate statement about evidence quality. A
high score from Sigma alone reads as a strong lead, never a confirmation.

**4. Capture the present tense.** Event logs describe what happened; they are silent about
what is happening. Step five captures the live system context, read-only: local accounts,
listening ports matched to their owning processes, anomalies at the disk root, and the
machine's logging coverage. That last one is underrated — a verdict is only worth what the
host agreed to record, and the console says so before you over-trust a clean timeline.

**5. Never expose the console.** Kage binds to 127.0.0.1 and ships no authentication, no
tokens, no TLS. That is a design decision, not an omission: the UI is meant to be reached
through an SSH tunnel or from the machine itself. The corollary is a rule you must not break:
never launch it with `--host 0.0.0.0`. A network-reachable triage console is a remote-control
forensic tool for anyone who finds the port.

## Demo mode: rehearse the whole chain

Before pointing anything at a real machine, run the demo. It generates a synthetic intrusion —
a phishing chain from a document to encoded PowerShell, a malware hash, Defender disabled, an
AMSI bypass, LSASS access, persistence, command-and-control to a hard-coded address with a
matching JA3 fingerprint, shadow copies deleted, the event log cleared, and a multi-gigabyte
exfiltration pattern — then buries it under 420 events of ordinary Windows noise. Fixed seed,
same story every run.

The chain runs end to end against that fiction; a full demo run lands at 80/100 with
CONFIDENCE HIGH — 441 events analysed. Nothing on your machine is read, scanned, or changed:
no evidence collection, no THOR, no Defender exclusion. On macOS and Linux the demo is the
default mode; on Windows it is one checkbox in Settings.

This is not a toy feature. It is how you learn where the buttons are, how you smoke-test a
deployment, and — as this kit demonstrates — how you produce screenshots and recordings of a
fully populated console without a single evidence-handling concern.

## Headless on the suspect host

Here is the workflow that makes Kage useful in practice. The machine needs examining; you
should not sit in front of it, and its owner should not lose their desktop for the duration.
Because the console is a local web app, it needs no desktop session at all.

The obstacle is elevation. An SSH session on Windows is non-interactive: UAC cannot prompt,
so `runas`-style elevation is impossible, and collection, the Defender exclusion, and THOR
all need administrator rights. The clean answer is a Task Scheduler task with `/RL HIGHEST` —
it runs with the account's highest privileges, elevated, with no prompt, hidden, and it
survives your disconnect:

```cmd
schtasks /Create /TN KageHeadless /TR "cmd /c cd /d C:\Kage && python -m dfirconsole" /SC ONCE /ST 23:59 /RL HIGHEST /F
schtasks /Run /TN KageHeadless
```

Then from your own machine:

```bash
ssh -N -o ServerAliveInterval=30 -o ExitOnForwardFailure=yes -L 8787:127.0.0.1:8787 user@host
```

Open <http://127.0.0.1:8787> locally and the entire console is there. The evidence never
moves: collection, timeline, report, and seals all stay on the host's disk, and only the live
stream travels. Copy the results off deliberately, over the same SSH channel, when you are
done — and document the hand-off.

The full reference — OpenSSH installation, the administrator vs standard-user
authorized-keys split, the task variants, the warm-up checklist, teardown (including the
Defender exclusion people forget to remove), and a troubleshooting table — is in the guide
repository.

## What this costs

Honesty about trade-offs is what separates architecture from advocacy:

- The score is a triage heuristic, not proof. Log-compression and caps keep it honest; read
  the per-axis breakdown and the confidence line, not the total.
- The chain is Windows-first. A Linux version is in progress upstream; on other platforms the
  demo is what runs.
- THOR Lite requires registration with Nextron and cannot be auto-downloaded. Without it, the
  YARA step is skipped — the chain carries on rather than failing.
- API keys are optional: without VirusTotal/AbuseIPDB or a model provider, enrichment
  contributes zero and the summary falls back to a deterministic local report.
- No authentication means the tunnel is the security boundary — and on a shared host, any
  local user could reach the console while it runs.
- One host per run, one workspace, no fleet view or case management. This is a fast,
  defensible first pass, not an enterprise platform.

## Steal the checklist

If you are about to triage a Windows host, this is the short version:

1. One chain, one workspace, one report — no tool sprawl.
2. Seal every step, and make each seal state what it covers.
3. Cap your scoring axes; state confidence separately from severity.
4. Capture live context read-only — ports, processes, accounts, log coverage.
5. Loopback only; tunnel for remote access; never expose the port.
6. Rehearse with synthetic evidence before the real run.
7. Elevate with a scheduled task, not a UAC prompt.
8. Leave originals in place; copy, never move; document the hand-off.

The guide repository documents all eight, with the reasoning written next to each workflow —
and its `content/` directory contains the kit that produced this article, including a
37-locale video pipeline narrated in 20 languages.

**Guide + kit: [github.com/baneeishaque-ompventure/kage-demo](https://github.com/baneeishaque-ompventure/kage-demo)**
**The tool: [github.com/karim852/Kage-DFIR-toolkit](https://github.com/karim852/Kage-DFIR-toolkit)**

Kage is created by k0r1m and released under the MIT license. This write-up is an independent
guide, not affiliated with or endorsed by the author. Point it only at hosts you own or are
explicitly authorized to examine.
