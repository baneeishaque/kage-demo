# Medium funnel kit

Traffic funnel for the **standalone Medium deep dive** —
[`medium-article.md`](medium-article.md) — which lives separately from the
repository-announcement kit in the parent [`content/`](../README.md) folder.

Everything here points readers at one destination: the Medium article. The article then
funnels readers to the guide repository.

## Asset map

| File                                               | Platform                | Format                              | Length           |
| -------------------------------------------------- | ----------------------- | ----------------------------------- | ---------------- |
| [`medium-article.md`](medium-article.md)           | Medium                  | Deep dive: design, trade-offs, code | ~2,000 words     |
| [`community-blog-post.md`](community-blog-post.md) | Community blog          | Companion post, warmer voice        | ~1,200 words     |
| [`linkedin.md`](linkedin.md)                       | LinkedIn                | Two posts: hook + engineering story | 150–220 words    |
| [`x-thread.md`](x-thread.md)                       | X / Twitter             | 10-post thread                      | 280 chars/post   |
| [`facebook.md`](facebook.md)                       | Facebook                | Group/Page post                     | ~220 words       |
| [`whatsapp.md`](whatsapp.md)                       | WhatsApp                | DFIR/admin-group messages + etiquette | 80–160 words   |
| [`instagram.md`](instagram.md)                     | Instagram               | 8-slide carousel + Reel caption     | Slides + caption |
| [`snapchat.md`](snapchat.md)                       | Snapchat                | Spotlight script                    | ~35 seconds      |
| [`reels.md`](reels.md)                             | Reels / Shorts / TikTok | Three short-form scripts            | 30–45 s each     |

## Publishing order

1. **Publish the Medium article first** — every asset below links to it, so it must resolve
   before anything else goes out.
2. **Community blog post** the same day (it is the soft-launch companion; it links to the
   Medium piece as "the full write-up").
3. **LinkedIn post 1** the next morning; post 2 two to three days later.
4. **X thread** the same day as LinkedIn post 1, a few hours later.
5. **Reels + Instagram carousel + Snapchat** midweek, all in one push — the short-form video
   drives the most cold traffic.
6. **Facebook** the day after the short-form push.
7. **WhatsApp groups** last and least often — treat it as a favour to communities, not a
   broadcast channel.

## Conventions

- **Medium URL placeholder** used throughout:
  `https://medium.com/@baneeishaque/kage-headless-dfir-triage`.
  If the published URL differs, find-and-replace it in every file here **before** posting
  anything.
- **UTM tags**: append
  `?utm_source=<platform>&utm_medium=social&utm_campaign=kage-headless-medium`
  to Medium links. The community blog post uses
  `utm_source=community-blog&utm_medium=referral`.
- **Repo URLs** (used inside the article and the community post): the guide + kit is
  `https://github.com/baneeishaque-ompventure/kage-demo`; the tool is
  `https://github.com/karim852/Kage-DFIR-toolkit`.
- **Verified numbers** quoted here: 11 chain steps, four capped score axes (45/25/20/10), 115
  tests, demo run 80/100 with 441 events analysed at high confidence, 37 subtitle languages
  and 20 narrations in the guide's video pipeline, version 1.5.0. Re-check against the guide
  repository before posting if the tool has moved on.
- **Independence**: the kit is an independent guide. Credit the author (k0r1m) wherever the
  tool is linked, and keep the "authorized hosts only" line in every long-form piece.
- **Screenshots** for carousel/short-form assets: capture them from a local demo run
  (`--demo`), which needs no real evidence. Suggested filenames: `shot-seals.png` (the SEALED
  lines with their bases), `shot-score.png` (four-axis breakdown), `shot-overview.png` (score
  gauge), `shot-chain.png` (chain-of-custody panel mid-run), `shot-terminal.png` (the
  `--demo` launch), `shot-timeline.png` (alerts/timeline).
