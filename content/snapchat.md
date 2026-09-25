# Snapchat Spotlight script

One 35-second vertical script (1080×1920). Spotlight favors raw, fast, subtitle-driven
content — screen recording with large captions outperforms produced edits.

## Concept: "Eleven steps, one console, 35 seconds"

Format: screen recording of the live demo with large burned-in captions. No voiceover needed;
add trending audio at low volume.

| Time      | Visual                                                                  | Caption (burned in)                          |
| --------- | ----------------------------------------------------------------------- | -------------------------------------------- |
| 0:00–0:03 | Terminal: `python -m dfirconsole --demo` types out                       | "Eleven steps."                              |
| 0:03–0:07 | Console opens on the Overview — the 80/100 gauge fills                   | "One console."                               |
| 0:07–0:12 | Chain-of-custody panel: steps flip to done, one after another            | "Collect → timeline → analyse. Live."        |
| 0:12–0:17 | Zoom on the SEALED lines with their seal hashes                          | "Every step sealed."                         |
| 0:17–0:21 | Closeup: "content of hayabusa-output.csv" / "execution record"           | "The seal says what it covers."              |
| 0:21–0:26 | Settings drawer: the Demonstration-mode checkbox, ticked                 | "Rehearse on fake evidence first."           |
| 0:26–0:31 | Terminal: the `schtasks` + `ssh -L` commands                             | "Then run it headless over SSH."             |
| 0:31–0:35 | GitHub: the guide repository README                                      | "Guide in the comments."                     |

## Caption for the Snap

***

Eleven steps, one console: collect → system context → Sigma timeline → analyse → YARA →
enrich → summary. Every step sealed with a SHA-256 that states what it covers.

Rehearse on a synthetic intrusion (80/100, 441 events) — then run it headless on the suspect
host over SSH. Evidence never leaves the disk.

Guide + kit: github.com/baneeishaque-ompventure/kage-demo

***

## Production notes

- **Subtitles are mandatory** on Spotlight — most viewing is muted. Burn them in at ~64px,
  high contrast, positioned in the lower third but above Snapchat's UI chrome.
- **Screen recording**: 60 fps capture of the browser at 1080×1920 (use a mobile-width
  viewport, ~390×844 CSS px, scaled up) so the text is legible on a phone.
- **The seal moment is the hook.** If the first three seconds do not land on the SEALED lines,
  re-cut — the hash block is the whole video.
- Record the terminal segment at a font size of at least 20 px; zoom the terminal rather than
  cropping.
- Do not use the trending audio's loud sections over the terminal shots — keep the command
  segment quiet so the text reads.
