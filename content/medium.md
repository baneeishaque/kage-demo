# Medium adaptation

Platform notes for publishing the canonical article on Medium.

## Metadata

- **Title:** One host, eleven sealed steps, zero tool sprawl: DFIR triage done properly
- **Subtitle:** A hash-sealed triage chain, a score you can audit, a rehearsal mode that
  touches nothing — and how to run it headless on the suspect host
- **Tags (max 5):** `dfir`, `incident-response`, `cybersecurity`, `windows`,
  `digital-forensics`
- **Canonical link:** set Medium's "Import a story" flow (or
  Settings → Publishing → Import) with your blog URL so the canonical tag points at the
  original. If publishing directly, add at the top: `Originally published at <blog-url>`.

## Publication checklist

1. Import via **medium.com/p/import** using the blog URL — Medium keeps formatting and sets
   the canonical automatically.
2. Add the subtitle in the post's subtitle field (not as body text).
3. Verify the five tags are set exactly as above.
4. Replace the repository URLs if they changed (find-and-replace
   `https://github.com/baneeishaque-ompventure/kage-demo` and
   `https://github.com/karim852/Kage-DFIR-toolkit`).
5. Insert the screenshots at the marked points below before publishing.
6. Publish, then paste the Medium URL back into the blog post as an "Also on Medium" line.

## Body

Use `blog-post.md` verbatim, with these Medium-specific adjustments:

- **Hero image** after the title (Medium shows a full-width lead image): use
  `shot-overview.png` — the demo console's overview with the 80/100 gauge.
- **Screenshot break 1** after "The decisions that matter" — insert `shot-seals.png` (the
  SEALED lines with their seal bases) and `shot-score.png` (the four-axis breakdown) as two
  inline images with the caption: *"A seal that states what it covers; a score you can audit
  axis by axis."*
- **Screenshot break 2** after the demo-mode section — insert `shot-chain.png` (the
  chain-of-custody panel mid-run) with the caption: *"Eleven steps, streamed live, sealed one
  by one — against synthetic evidence, on a laptop, with nothing at risk."*
- **Code blocks**: Medium renders fenced blocks as preformatted text; the `schtasks`, `ssh`,
  and seal-output samples survive intact. The seal block's alignment may collapse — acceptable.
- **Bold repository links** render as plain links on Medium; keep them on their own line so
  they read as call-to-action rows.
- **Credit line**: keep the closing "created by k0r1m … independent guide" paragraph — Medium
  readers click through to the tool, and the independence note prevents confusion.

## Cross-promotion

- Add a footer line: *"This article is part of a full content kit — the guide repository's
  `content/` directory contains the LinkedIn, X, and short-form versions, plus a 37-locale
  video pipeline."*
- Do not enable the Medium paywall for this piece; the goal is reach.
- After 48 hours, check the Medium stats and note the traffic split vs the canonical — if
  Medium outperforms by more than 3x, consider making it the canonical instead and point the
  blog at it.
