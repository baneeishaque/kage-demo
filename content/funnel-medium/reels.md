# Reels / Shorts / TikTok — three scripts

Three short-form scripts, 30–45 seconds each, all pointing at the Medium article (link in bio;
on Shorts, put the article link in the description).

## Reel 1 — "The seal that states its basis" (35 s)

| Time      | Visual                                                        | VO / caption                                                                  |
| --------- | ------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| 0:00–0:04 | Terminal: a step finishing, a SEALED line appearing           | "Most triage pipelines can't prove what happened between steps."              |
| 0:04–0:10 | Zoom on `seal 31d1991a65598de8 — content of hayabusa-output.csv` | "This one seals every completed step with a SHA-256 — and the seal says what it covers." |
| 0:10–0:17 | Zoom on `seal 8f2c04b71ae93d55 — execution record (no file produced)` | "No file produced? The seal says that too, instead of implying more than it can prove." |
| 0:17–0:25 | Code zoom: the `_seal` function hashing artefacts             | "The hash folds the artefact contents into one digest — rerun it a week later and you have proof." |
| 0:25–0:32 | Chain panel: all 11 steps sealed, green                       | "Eleven steps. Every one sealed. Every basis stated."                         |
| 0:32–0:35 | End card: "Full write-up — link in bio"                       | —                                                                             |

## Reel 2 — "A score you can argue with" (40 s)

| Time      | Visual                                                          | VO / caption                                                                 |
| --------- | --------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| 0:00–0:05 | A gauge showing 87/100 with no breakdown, then it cracks        | "'Threat score: 87' is a number nobody can audit."                           |
| 0:05–0:12 | Four-axis breakdown: severity 45, chain 25, reputation 20, corroboration 10 | "So this score is four independent axes, each capped."         |
| 0:12–0:20 | Code zoom: `6.5 * (raw ** 0.5)` and the caps                    | "Severity is log-compressed — a thousand noisy events can't fake a verdict." |
| 0:20–0:30 | Verdict lines: same 80/100 at high vs low confidence            | "And confidence is a separate line. Same number, two honest sentences."       |
| 0:30–0:37 | Demo run: 80/100, CONFIDENCE HIGH, 441 events analysed          | "Rehearse it all on synthetic evidence — 80/100, confidence high, nothing touched." |
| 0:37–0:40 | End card: "Full write-up — link in bio"                         | —                                                                            |

## Reel 3 — "Triage without disturbing the desk" (45 s)

| Time      | Visual                                                          | VO / caption                                                                 |
| --------- | --------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| 0:00–0:06 | A desk in use; a "triage needed" sticky note                    | "This machine needs triage today. Its owner has a deadline."                  |
| 0:06–0:14 | Host terminal: schtasks command with `/RL HIGHEST`              | "On the host: one scheduled task — elevated, hidden, no UAC prompt, survives your disconnect." |
| 0:14–0:22 | Your laptop: the ssh tunnel command, then the console opening   | "On your laptop: one SSH tunnel. The whole console is there."                 |
| 0:22–0:32 | Console: collection streaming; evidence folder on the host      | "The evidence never moves. Collection, timeline, report, seals — all on the host's disk." |
| 0:32–0:40 | Repo page + docs folder                                         | "Architecture, trade-offs, and line-by-line explainers — all public, MIT."    |
| 0:40–0:45 | End card: "Full write-up — link in bio"                         | —                                                                            |

## Production notes

- 9:16 vertical; burn captions; assume muted autoplay.
- Code shots: dark background, monospace, one highlighted line at a time, 2–3 seconds per shot
  maximum.
- Reuse the same footage library across all three reels (demo chain streaming, seal lines,
  score breakdown, schtasks + ssh terminals) — only the script changes.
- Publish Reel 1 first; if it performs, follow with 2 and 3 in the same week.
- All footage comes from demo mode — never real evidence, hostnames, or case data.
