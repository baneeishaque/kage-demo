# Short-form video scripts (Reels / Shorts / TikTok)

Three scripts, 30–45 seconds each, all vertical (1080×1920). Each is designed to work muted
with burned-in captions and to be shootable from a screen recording plus one talking-head
segment.

## Script 1 — "The seal moment" (35 s)

**Hook (0:00–0:03):** Screen shows the SEALED lines in the chain panel.
**Caption:** *"This is what makes a triage report defensible."*

| Time      | Shot                                                                 | Caption                                               |
| --------- | -------------------------------------------------------------------- | ----------------------------------------------------- |
| 0:03–0:08 | Zoom on `seal 31d1991a65598de8 — content of hayabusa-output.csv`      | "Every completed step gets a SHA-256."                |
| 0:08–0:13 | Cut to `seal 8f2c04b71ae93d55 — execution record (no file produced)`  | "And the seal says what it covers."                   |
| 0:13–0:19 | Chain panel: 11 steps flipping to done                                | "Eleven steps. One console. No shoebox of tools."     |
| 0:19–0:25 | Overview: 80/100 gauge, then the four-axis breakdown                  | "Four capped score axes. Confidence kept separate."   |
| 0:25–0:31 | Terminal: `schtasks` + `ssh -L` commands                              | "Then run it headless — evidence never leaves disk."  |
| 0:31–0:35 | GitHub repo, README hero, link overlay                                | "Full guide + kit — link in bio."                     |

**Caption text for the post:**

***

A triage report is only defensible if you can prove the evidence wasn't altered.

Every completed step in this console is sealed with a SHA-256 — and the seal states its basis:
"content of hayabusa-output.csv" is verifiable; "execution record (no file produced)" is
honest. Same mechanism, two different claims, both explicit.

11 steps, 1 console, 115 tests. Link in bio.

```text
# dfir #cybersecurity #incidentresponse #blueteam #forensics
```

***

## Script 2 — "Why your triage isn't defensible" (40 s)

**Hook (0:00–0:04):** Talking head or text-on-screen.
**Caption:** *"Three triage problems that show up in every incident — and the fixes."*

| Time      | Shot                                                                | Caption                                                         |
| --------- | ------------------------------------------------------------------- | --------------------------------------------------------------- |
| 0:04–0:14 | Bug 1: a folder of tool outputs, mismatched formats                 | "Problem 1: tool sprawl. Every hop loses context."              |
| 0:14–0:20 | Fix: the 11-step chain panel                                        | "Fix: one chain, one workspace, one report."                    |
| 0:20–0:30 | Problem 2: "trust me, I ran the tools" — no hashes anywhere         | "Problem 2: nothing proves the evidence stayed untouched."      |
| 0:30–0:36 | Fix: the SEALED lines with seal bases                               | "Fix: seal every step; state what each seal covers."            |
| 0:36–0:40 | Repo link overlay                                                   | "Both fixes, plus the headless workflow: link in bio."          |

**Caption text for the post:**

***

The three triage problems I see most:

1. Tool sprawl — collect with one tool, timeline with another, scan with a third; the analyst
   becomes the integration layer
2. No chain of custody — a report with no hashes proves nothing about the evidence afterwards
3. Elevation dead-ends — you can't sit in front of the host, and SSH can't prompt for UAC

All three have structural fixes: one sealed chain, seals that state their basis, and a
scheduled task with `/RL HIGHEST` for elevation without a prompt.

Full guide in bio.

```text
# dfir #incidentresponse #cybersecurity #blueteam #windows
```

***

## Script 3 — "Demo mode tour" (45 s)

**Hook (0:00–0:04):** Terminal, cursor blinking.
**Caption:** *"What a full DFIR chain looks like — with nothing at risk."*

| Time      | Shot                                                                 | Caption                                                              |
| --------- | -------------------------------------------------------------------- | -------------------------------------------------------------------- |
| 0:04–0:12 | `python -m dfirconsole --demo`; the console opens                     | "Demo mode generates a synthetic intrusion."                         |
| 0:12–0:20 | Chain runs: steps seal one by one                                     | "The whole 11-step chain runs — sealed, streamed, replayable."       |
| 0:20–0:30 | Overview: 80/100, CONFIDENCE HIGH, 441 events; alerts + ATT&CK views  | "80/100 on 441 events — with 420 noise events hiding the story."     |
| 0:30–0:38 | Settings: the Demonstration-mode checkbox                             | "It reads nothing. Scans nothing. Changes nothing."                  |
| 0:38–0:45 | Repo + docs tree on GitHub                                            | "Full guide, kit, and the tool. Link in bio."                        |

**Caption text for the post:**

***

What does a full DFIR triage chain actually look like?

Demo mode generates a synthetic intrusion — encoded PowerShell, LSASS access, C2 with a JA3
match, shadow copies deleted — then buries it under 420 events of ordinary noise. The entire
11-step chain runs against that fiction: sealed, streamed, replayable.

Result: 80/100, CONFIDENCE HIGH, 441 events analysed. And it read nothing, scanned nothing,
changed nothing on the machine.

Rehearse on fake evidence before the real run. Link in bio.

```text
# dfir #cybersecurity #incidentresponse #forensics #infosec
```

***

## Production notes

- Record the browser at a phone-width viewport (~390×844 CSS px) scaled to 1080×1920; desktop
  layouts are unreadable on phones.
- Burn captions at ~64 px with a solid backing rectangle; keep them in the middle third to
  clear platform UI.
- Scripts 1 and 3 need only a screen recorder; script 2 works best with a talking head for the
  hook, then screen capture.
- Keep each cut under 5 seconds — these scripts have 7–8 cuts each for a reason.
- Export at 1080×1920, H.264, 8–10 Mbps; platforms re-encode aggressively.
