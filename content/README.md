# Content kit

Everything needed to present the Kage field guide: one canonical long-form article,
platform-specific adaptations, and a multilingual video pipeline.

## Asset map

| File                            | Platform                     | Format                              | Length           |
| ------------------------------- | ---------------------------- | ----------------------------------- | ---------------- |
| [`blog-post.md`](blog-post.md)  | Own blog / dev.to / Hashnode | Canonical article (source of truth) | ~1,800 words     |
| [`medium.md`](medium.md)        | Medium                       | Adapted article + metadata          | ~1,500 words     |
| [`linkedin.md`](linkedin.md)    | LinkedIn                     | Two posts: announcement + deep dive | 130–220 words    |
| [`x-thread.md`](x-thread.md)    | X / Twitter                  | 12-post thread                      | 280 chars/post   |
| [`facebook.md`](facebook.md)    | Facebook                     | Group/Page post                     | ~180 words       |
| [`whatsapp.md`](whatsapp.md)    | WhatsApp                     | Community broadcast + Status        | 60–120 words     |
| [`instagram.md`](instagram.md)  | Instagram                    | 10-slide carousel + Reel caption    | Slides + caption |
| [`snapchat.md`](snapchat.md)    | Snapchat                     | Spotlight script                    | 35 seconds       |
| [`reels.md`](reels.md)          | Reels / Shorts / TikTok      | Three short-form scripts            | 30–45 s each     |
| [`youtube/`](youtube/)          | YouTube                      | Full video pipeline (37 locales)    | ~4 minutes       |

## Publishing order

1. **Publish the repository first** — every post links to it.
2. **Blog post** on the canonical domain (or dev.to/Hashnode), then syndicate to Medium with a
   `rel=canonical` pointing at the original.
3. **LinkedIn announcement** the same day; the deep-dive post 2–3 days later.
4. **X thread** the next morning; quote-post the seal block.
5. **Instagram carousel + Reels** midweek; Snapchat Spotlight the same day as Reels.
6. **Facebook** and **WhatsApp** last — they reach people who already know you; give them the
   polished artifact, not the announcement.
7. **YouTube video** whenever the multilingual cut is ready; it is the only asset that shows
   all 37 locales in one artifact.

## Conventions

- **Repo URLs used throughout**: the guide +
  kit is `https://github.com/baneeishaque-ompventure/kage-demo`; the tool is
  `https://github.com/karim852/Kage-DFIR-toolkit`. If the published URLs differ,
  find-and-replace before posting.
- **UTM tags**: append `?utm_source=<platform>&utm_medium=social&utm_campaign=kage-launch` to
  links (the blog post uses `utm_source=blog&utm_medium=referral`).
- **Numbers** quoted in the kit (11 chain steps, 115 tests, version 1.5.0, demo score
  80/100 with 441 events analysed, 37 video locales, 20 narrations) were verified against
  Kage 1.5.0; re-check before posting if the tool has moved on.
- **Independence**: the kit is an independent guide. Credit the author (k0r1m) wherever the
  tool is linked, and keep the "authorized hosts only" line in every long-form piece.
- **Screenshots** for carousel/X posts: capture them from a local demo run (`--demo`), which
  needs no real evidence. Suggested filenames: `shot-overview.png` (score gauge), `shot-chain.png`
  (chain-of-custody panel), `shot-seals.png` (the SEALED lines), `shot-score.png` (four-axis
  breakdown), `shot-timeline.png` (alerts/timeline), `shot-system.png` (system context),
  `shot-summary.png` (the written summary), `shot-terminal.png` (the `--demo` launch).

## Video pipeline (YouTube)

`youtube/` contains a scripted pipeline rather than a finished upload:

- `translations/<locale>.json` — per-locale slide titles and narrations (37 files; the
  community-reviewable layer).
- `build-scenes.py` — owns scene order, on-screen code, the locale roster, and voices;
  assembles `scenes.json`.
- `scenes.json` — generated single source of truth consumed by the renderer and builder.
- `render-slides.mjs` — renders slide images with headless Chromium (per-script fonts, RTL).
- `build-video.py` — generates narration with macOS `say`, assembles per-locale videos, muxes
  the master multi-audio MKV (20 audio + 37 subtitle tracks), and exports subtitles.
- `subtitles/` — generated SRT/VTT files for all 37 locales, committed as deliverables.
- `metadata.md` — titles, descriptions, tags, and chapters per locale.

See [`youtube/README.md`](youtube/README.md) for the full pipeline, the locale roster, and
the 17 subtitle-only locales.
