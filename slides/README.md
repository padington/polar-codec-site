# Slides: "Can polar coordinates make round videos smaller?"

Two copies of the same 24-slide deck, written for a smart non-specialist.

| file | what it is |
|---|---|
| `index.html` | self-contained HTML deck (own CSS/JS, no libraries, no network). Videos and PNGs load from `media/`. |
| `polar-codec.pptx` | the same slides for PowerPoint / Keynote, 16:9, static pictures; video slides show still frames and point to the HTML deck. 25 slides: HTML slide 13 becomes two (the test-grid sheet and the real-frame sheet). |
| `media/` | demo videos (re-encoded copies), still frames, rendered figures. `media/shapes/` holds the canvas samples of slide 13, written by `scripts/shape_samples.py`. |
| `figures/` | the SVG diagrams that are inlined in `index.html` and rasterised into `media/*.png` for the PPTX. |
| `build.py` | regenerates everything above from `results/` and `summary.json`. |

## How to open

**HTML:** double-click `index.html` (any modern browser; works from the file system).

- Next slide: `→`, `↓`, `Space`, `PageDown`, `Enter`, or click the right three quarters of the screen.
- Previous slide: `←`, `↑`, `PageUp`, `Backspace`, or click the left quarter.
- `Home` / `End` jump to the first / last slide; `#13` in the URL opens slide 13 directly.
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
12. Other ways to unroll the disc (shape sweep: 6 canvases x 12 clips x 2 codecs, one table, the corner-cost ceiling, one-line takeaway)
13. What the shapes look like (the `shapes_grid.png` contact sheet: a synthetic test disc drawn on all nine canvases, five canvas videos, a link to the real-frame sheet). Two PPTX slides: the grid sheet, then `shapes_real.png`.
14. Synthetic motion tests: only a fast spin wins
15. Where rotation and zoom already pay: AV1 / VVC motion tools, literature numbers
16. AV1 result: the gap narrows, the verdict holds (35 words, one table: real clips 1.82x -> 1.76x and 1.37x -> 1.19x, fast spin -14 % -> -34 %, libaom warps off / on -32 % / -21 %, square +20 % without them; one-line takeaway)
17. What others tried: polar in video, 1 of 2 (3 sources, one line each)
18. What others tried: polar in video, 2 of 2 (3 sources + "none beat the square" badge)
19. What others tried: polar for still images, 1 of 2 (3 sources)
20. What others tried: polar for still images, 2 of 2 (3 sources + badge)
21. Should we build a polar still-image coder first? (verdict)
22. Three ways to use polar maths from here + recommendation
23. Appendix: the numbers (table generated from `results/batch2/summary.json`)
24. Appendix: method and links

## Rebuilding

```bash
.venv/bin/python docs/slides/build.py
```

Needs the project venv (numpy, cv2, python-pptx), `ffmpeg` and `rsvg-convert` on PATH.
The script asserts the headline numbers against `results/batch2/summary.json`,
`results/batch2/synth/synth_report.md`, for slide 12 `results/batch4_shapes/summary.json`
+ `results/batch4_shapes_svtav1/summary.json` (and the ceiling phrases in
`results/batch4_shapes/shapes_report.md`), and, for slide 16, `results/batch2_svtav1/av1_gap.json`
(written by `python -m polarcodec av1-gap`), so a changed benchmark fails the build
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
  crf-24 median (~0.5 Mbit/s = 494 kbps), which the slide now states. `README.md` and
  `docs/explainer.md` now use the same anchor (1.9 / 2.5 / 3.4 MB: 494 kbps x 30 s / 8 =
  1.85 MB, x 1.82 = 3.37 MB, rounded; the deck truncates to 1.8 / 3.3).
- Slide 16 (formerly the "AV1 result: pending" placeholder) is filled from
  `results/batch2_svtav1/av1_gap.json`: the `av1` entry in `build.py`'s `SLIDES` list
  reads the medians (polar_eq / ellip_full under svtav1, rot3 under both codecs, the
  libaom warps-on / warps-off BD-rates) and the build asserts them against the numbers
  quoted in the README. The square's "+20 %" is the BD-rate of square-with-warps-off vs
  square-with-warps-on, i.e. the square needs 20 % *more* bytes without the warps
  (about 16 % fewer with them), never "20 % fewer with them". Slide 22's second card
  and slide 5's codec box no longer say the AV1 test is pending.
- Guard rows read 1.92x in the deck (median +92.46 %); the README's rounded "+92.5 %"
  gives 1.93x if rounded twice.
- Slide 12 (the shape sweep) is built from the two `batch4_shapes` summaries: the six canvas
  rows are `SHAPE_ROWS` in `build.py` and every percentage is the median BD-rate `psnr_disc`
  read from `summary.json`, asserted against the numbers quoted in `README.md` and in
  `results/batch4_shapes/shapes_report.md`. `hybrid_center` was never run under SVT-AV1, so its
  AV1 cell reads "not run". The slide reuses the `av1` slide kind (table + one-line takeaway)
  with two extra fields: `ceiling` (the muted line under the table) and `col_w` / `orange_cols`
  for the PPTX table.
- Slide 13 (the canvas gallery) does **not** generate its own media: `scripts/shape_samples.py` writes
  `media/shapes/` (per-preset canvas / round-trip / error / test-grid PNGs, two 2000-px contact sheets
  and five 4-second canvas videos) and `build.py` only asserts that the files it references exist,
  failing with the command to run if they do not. Re-run the sample script after any change to
  `polarcodec/maps.py` or to the shape-sweep results, then re-run `build.py`. The PPTX embeds
  downscaled JPEG copies of the two sheets (a temporary directory, never committed) so the file stays
  under the 8 MB limit of the public site.
