---
name: short-form-video
description: "Use when rendering vertical short video with captions."
version: 1.0.0
author: Justin Tutterow
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [video, ffmpeg, shorts, captions, pillow]
    related_skills: [markdown-to-pdf, manim-video]
---

# Short-form video — 1080x1920 with burned-in captions

Build vertical shorts from a text spec plus optional screenshots and voice-over. The procedure below
is complete enough to rebuild the renderer from scratch. Default look: near-black `#0B0D10`
background, gold `#E8B84B` accents, white text, a small left-aligned watermark (yours, set per brand),
and a thin accent progress bar along the bottom edge.

## When to Use

- Any 9:16 clip for Shorts / Reels / TikTok built from cards, screenshots and captions.
- Episode series where each scene is one spoken line.
- **Not** for long-form landscape video (use a real NLE or `manim-video` for animation).

## Procedure

1. **Spec the episode as JSON**: `{"slug", "scenes": [{type, kicker, text, sub, image, seconds}]}`
   with `type` in `hook` | `point` | `end`. One scene = one spoken line, budget ~2.5 words/second.
2. **Render one PNG per scene with Pillow.** If the scene's image file exists, cover-crop it to
   1080x1920, darken to ~0.58 brightness and overlay a bottom gradient (bottom ~45% only) so the
   caption stays legible; otherwise draw a flat card. **Never block on missing assets** — text
   cards make the episode watchable before a single screenshot exists.
3. **Assemble with looped inputs + the concat filter** (not the concat demuxer — see pitfalls):
   ```
   ffmpeg -y \
     -loop 1 -framerate 30 -t 5 -i f001.png \
     -loop 1 -framerate 30 -t 6 -i f002.png \
     -filter_complex "[0:v]scale=1080:1920,setsar=1,fps=30[v0];[1:v]scale=1080:1920,setsar=1,fps=30[v1];[v0][v1]concat=n=2:v=1:a=0[out]" \
     -map "[out]" -c:v libx264 -crf 19 -pix_fmt yuv420p -r 30 -movflags +faststart out.mp4
   ```
4. **Voice-over:** per-scene takes `audio/01.wav`, `02.wav`… are placed at the cumulative scene
   offsets over a silent bed of the full length (`anullsrc` + `adelay=<ms>|<ms>` per input +
   `amix=duration=first`), so a short take stays in its slot instead of dragging the edit. A
   single continuous take can be muxed instead with `-shortest`.
5. **Verify (never skip):** `ffprobe -show_entries format=duration` must equal the sum of scene
   seconds; extract one frame per scene (`ffmpeg -ss <midpoint> -frames:v 1`) and check each with
   `vision_analyze` for legibility, overlap and clipping. Sub-second drift is fine, seconds are a bug.
   - A still that doubles as a cover or product image needs a second look at **320 px wide**: small
     type vanishes at thumbnail size, and the thumbnail is the audience that decides the click. Judge
     at the size it will actually be seen, not the size you rendered it.

## Pitfalls

- **ffmpeg 9 removed `-vsync`** — it exits "Unrecognized option 'vsync'". Don't reach for
  `-fps_mode vfr` either: combined with `-r` it fails with "One of -r/-fpsmax was specified
  together with a non-CFR -vsync/-fps_mode". For stills you need neither; loop each image and let
  the concat filter join at a fixed `-r 30`.
- **The concat demuxer drifts on stills.** `file x.png` + `duration N` entries resampled from a
  25 fps PNG input produced ~36 s for a 30 s timeline. It silently fails verification, so use the
  filter approach above.
- **Pillow is not in the Hermes python.** `python3 -c "import PIL"` fails; `/usr/bin/python3` has
  it. Invoke the renderer as `/usr/bin/python3 render.py …`.
- **Fonts:** DejaVu Sans and Liberation are installed; Inter is not. Design with what exists or
  install deliberately — a silent fallback ships a different look than the rest of the brand.
- **Keep captions out of platform UI zones:** left-align with ~72 px margins, text in the lower
  third and clear of the bottom ~120 px where apps draw their own controls; the progress bar is
  decorative and may sit underneath.
- **Every on-screen number must come from an official patch note or the linked guide.** Captions
  are published claims; apply the same sourcing rule as a guide page.
- **Only the user's own screenshots.** No press kits, no other people's footage.

## Shipping it to someone else

Handing the renderer to anyone else — selling it, open-sourcing it — changes three things, and each
one is invisible while you only run it on the build box:

- **Font paths must be discovered, not assumed.** A hardcoded `/usr/share/fonts/...` list works on
  one machine and nowhere else; a Windows or macOS buyer crashes on first run. Bundle a font in `assets/fonts`
  and fall back through platform directories, with a readable error when nothing matches.
- **Pillow and ffmpeg are your dependencies, not theirs.** Pick one: bundled binary, setup script, or
  Docker wrapper — and test on a clean machine. The build box proves nothing about installability.
- **Docs are part of the artifact:** install, first render, and the three ways it fails (screenshot
  the wrong aspect, missing audio file, text overflow).

Don't start this hardening on spec. Bundle fonts, write `setup.md`, and stop there until someone has
actually agreed to pay for it — roughly two evenings, worth none of them before demand.

## Writing episodes

Each episode folder: `episode.json` (timings/captions), `script.md` (one line per scene, plus a
record-scene-by-scene note), `shots.md` (exact capture list with a text-card fallback). If a take
runs long, trim the line or raise that scene's `seconds` — never speed up the audio.
