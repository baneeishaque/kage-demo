# Instagram

A 10-slide carousel (the format that performs for technical content), a Reel caption, and
hashtag sets. Slides are text-over-screenshot; keep each slide to one idea.

## Carousel: slides and copy

Design: 1080×1350 (4:5). Dark background (`#0f172a`), white text, one screenshot per slide
where indicated. Slide 1 is the hook — it must work with zero context.

| #  | On-slide text                                                                     | Visual                                     |
| -- | --------------------------------------------------------------------------------- | ------------------------------------------ |
| 1  | Most "DFIR triage" is a folder of batch files and hope.                            | None — pure hook                           |
| 2  | So I wrote up the 11-step chain that isn't: one console, one report.               | `shot-terminal.png`                        |
| 3  | Collect → timeline → analyse → scan → enrich → summary. Live-streamed.             | `shot-chain.png`                           |
| 4  | Every completed step is sealed — and the seal says what it covers.                 | `shot-seals.png`                           |
| 5  | "content of hayabusa-output.csv" vs "execution record". Two claims. Both explicit. | Seal closeup                               |
| 6  | The score is 4 capped axes. No single source can dominate it.                      | `shot-score.png`                           |
| 7  | Confidence is separate from severity. A strong lead is not a confirmation.         | `shot-overview.png`                        |
| 8  | Rehearse first: demo mode runs the whole chain on a synthetic intrusion. Reads nothing. | `shot-timeline.png`                   |
| 9  | Then headless on the suspect host: scheduled task + SSH tunnel. Evidence stays on disk. | Terminal: ssh command                  |
| 10 | Guide + kit + the tool (MIT, by k0r1m). Authorized hosts only. Link in bio.        | `shot-summary.png`                         |

## Caption (carousel)

***

Most "DFIR triage" is a shoebox of tools and a prayer. One tool collects, another builds the
timeline, a third scans — and nothing proves the evidence stayed untouched.

I wrote up an independent guide + kit for Kage, an open-source (MIT) Windows triage console
that runs the whole chain as one sealed sequence:

- 11 steps in one browser tab, streamed live and refresh-safe
- Every completed step sealed with a SHA-256 that states its basis — file content, or the
  execution record alone
- A score on four capped axes, with confidence kept separate from severity
- Demo mode that runs the entire chain against a synthetic intrusion (80/100, 441 events)
  without touching the machine
- Headless over SSH: elevated via a scheduled task, reached through a tunnel, evidence never
  leaving the host's disk

Swipe through for the architecture decisions that made it work.

Guide + kit: link in bio.

***

```text
# dfir #incidentresponse #cybersecurity #blueteam #forensics #windows #infosec #malware
# digitalforensics #soc #threathunting #securityengineering #python #opensource #techtok
```

## Reel caption (pairs with `reels.md` script 1)

***

POV: you hand over a triage report where every step carries a SHA-256 and the seal says
exactly what it covers.

Eleven steps. One console. Zero "trust me, I ran the tools".

Full guide: link in bio.

***

```text
# dfir #cybersecurity #incidentresponse #blueteam #forensics #infosec #soc #techtok
```

## Posting notes

- Carousel: post Tuesday–Thursday, 11:00–13:00 local. Reels: 19:00–21:00.
- Alt text for every slide (accessibility + reach): e.g. slide 4 → *"Two sealed steps from
  the console's chain-of-custody panel, each showing a SHA-256 and what the seal covers."*
- First comment: *"What does your triage report prove about the evidence afterwards? Mine
  used to prove nothing."* — comment bait that is genuinely on-topic.
- Keep the link out of the caption (Instagram suppresses link posts); use "link in bio" and a
  pinned story with the tap-through.
