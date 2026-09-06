# Slides: "Can polar coordinates make round videos smaller?"

Two copies of the same 22-slide deck, written for a smart non-specialist.

| file | what it is |
|---|---|
| `index.html` | self-contained HTML deck (own CSS/JS, no libraries, no network). Videos and PNGs load from `media/`. |
| `polar-codec.pptx` | the same slides for PowerPoint / Keynote, 16:9, static pictures; video slides show still frames and point to the HTML deck. |
| `media/` | demo videos (re-encoded copies), still frames, rendered figures. |
| `figures/` | the SVG diagrams that are inlined in `index.html` and rasterised into `media/*.png` for the PPTX. |
| `build.py` | regenerates everything above from `results/` and `summary.json`. |

## How to open

**HTML:** double-click `index.html` (any modern browser; works from the file system).

- Next slide: `→`, `↓`, `Space`, `PageDown`, `Enter`, or click the right three quarters of the screen.
- Previous slide: `←`, `↑`, `PageUp`, `Backspace`, or click the left quarter.
- `Home` / `End` jump to the first / last slide; `#12` in the URL opens slide 12 directly.
- Videos autoplay muted on the slide that shows them (controls are on; click a video to pause it).
- Print (`Cmd/Ctrl+P`): one slide per page, dark background kept; videos are omitted on paper.

**PPTX:** open `polar-codec.pptx`. Slide 8 has stills only (and no still of the
original clip, see Notes); the moving versions are in the HTML deck.

## Slide list

1. Title: can polar coordinates make round videos smaller?
2. What a Telegram circle really is (square file + round mask)
3. Where everything is square, and why (sensor to screen)
4. The polar idea: unroll the disc into a strip
5. What we built (unroll, unchanged codec, roll back)
6. How we judged: same picture quality, count the bytes (PSNR, bytes at equal quality, 30-s example)
7. Result: bytes relative to the square for six variants, with confidence intervals
8. See it: original, square, polar as circles + the strip; a gallery pair
9. Why it loses #1: rim starved, centre flooded (-3.0 dB rim vs -0.6 dB centre)
10. Why it loses #2: motion becomes a warp; white corners are already almost free
11. What we tried to rescue it (guard rows, seam at the chin, equal-area bands, gentler maps)
12. Synthetic motion tests: only a fast spin wins
13. Where rotation and zoom already pay: AV1 / VVC motion tools, literature numbers
14. **AV1 result: pending** (placeholder, to be filled from `results/batch3_av1`)
15. What others tried: polar in video, 1 of 2 (3 sources, one line each)
16. What others tried: polar in video, 2 of 2 (3 sources + "none beat the square" badge)
17. What others tried: polar for still images, 1 of 2 (3 sources)
18. What others tried: polar for still images, 2 of 2 (3 sources + badge)
19. Should we build a polar still-image coder first? (verdict)
20. Three ways to use polar maths from here + recommendation
21. Appendix: the numbers (table generated from `results/batch2/summary.json`)
22. Appendix: method and links

## Rebuilding

```bash
.venv/bin/python docs/slides/build.py
```

Needs the project venv (numpy, cv2, python-pptx), `ffmpeg` and `rsvg-convert` on PATH.
The script asserts the headline numbers against `results/batch2/summary.json`
and `results/batch2/synth/synth_report.md`, so a changed benchmark fails the build
instead of silently shipping stale slides. Existing files in `media/` are reused;
delete one to regenerate it.

## Notes

- The original clip is personal and is never published. `media/original_round_demo.mp4`
  and the still cut from it, `media/still_original_round_demo.png`, are both excluded
  from git by `docs/slides/.gitignore` (`media/original_*.mp4`, `media/still_original_*.png`).
  The PPTX never embeds the original: its slide 8 shows a labelled empty circle
  ("original clip not published"). In a fresh clone the HTML deck does the same: the
  `<video>` `onerror` handler swaps in the same label. All other media are re-encoded
  derivatives of decoded outputs.
- The 30-s worked example on slide 6 (1.8 / 2.5 / 3.3 MB) is anchored on the square
  crf-24 median (~0.5 Mbit/s), which the slide now states. `README.md` and
  `docs/explainer.md` use 1.0 / 1.4 / 1.8 MB for the same ratios (a different base);
  those files belong to the other workflow and should be aligned to one anchor.
- Slide 14 is a deliberate placeholder for the AV1 batch. Fill it by replacing the
  dashed box in `index.html` (`id="av1-placeholder"`) and the `placeholder` entry in
  `build.py`'s `SLIDES` list.
- Guard rows read 1.92x in the deck (median +92.46 %); the README's rounded "+92.5 %"
  gives 1.93x if rounded twice.
