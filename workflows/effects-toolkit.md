# Effects toolkit: local tools and recipes

All tools run locally from the project folder (`.venv\Scripts\python.exe core\scripts\<tool>.py ...`). Animated graphics cannot be keyframed through the Resolve API, so they are pre-rendered as short clips (ProRes 4444 with transparency for overlays) and placed on a video track above the footage. Always render with motion blur.

## Time-lapse
`ffmpeg -ss <start> -t <length> -i clip -an -vf "setpts=PTS/<speed>,fps=30" -t <seconds> -c:v h264_nvenc -preset p5 -cq 19 -b:v 0 out.mp4`. Speed = source seconds / wanted seconds (7 min into 3.5 s = 120x; 6 min into 2 s = 187x). Do not scale vertical phone footage to a fixed 1920x1080 (it distorts); keep the native 1080x1920. Pre-rendered clips have no extra frames for transitions (see `davinci-playbook.md`). When one time-lapse is split in two pieces in the edit, zoom keys continue across the split (for example 1.00 to 1.07, then 1.07 to 1.14).

## Picture cards: `slide_cards.py` and `overlay_anim.py`
- `slide_cards.py <out.mov> <img1> [img2 ...] [--frames 46] [--fade-in 13] [--fade-out 12] [--width 600]`: cards slide in from alternating sides, hold, slide out (quartic ease-out with a 3% settle, in-out cubic exit, 8 sample motion blur).
- `overlay_anim.py <spec.json> <out.mov>`: general builder. Elements are `card` (picture), `text` (one line, or stacked `lines` with sizes) and `flash`. Entrances: `pop` (scale with overshoot and fade), `slide-left`/`slide-right` (with `slide_dist`), `slam` (text starts huge and blurred, lands hard, shakes). Exits: `pop` or slide. Per-element `start`/`end` frames, `angle`, `x`/`y` centre, `w` (cards) or `size` (text), `subframes` for motion blur (6 normal, 24 for very fast moves). Ready-made specs are in `core/templates/overlay_specs/`.
- Cards: white rounded card, 10 to 18 px padding, soft shadow, tilt -5 to +4 degrees. Transparent pictures are put on white (decode AVIF/WebP with Pillow because ffmpeg drops AVIF alpha).
- **Flat accent text (preferred):** give `runs` to colour parts of one line, e.g. `"runs":[{"text":"HAIR ","fill":[255,255,255]},{"text":"DRYER","fill":[255,205,40]}]`, plus `"stroke":[18,18,18]`, `"stroke_w":5` (thin outline) and `"font":"C:/Windows/Fonts/seguibl.ttf"` (Segoe UI Black; use forward slashes in JSON). Stacked `lines` take the same keys per line. Examples: `core/templates/overlay_specs/*_accent*.json`.
- Text colours (gradients, glow): per text element or per stacked line give `gradient` (`[[r,g,b] top, [r,g,b] bottom]`) or `fill`, `stroke` (outline colour), `glow` (halo colour) and `shadow`. Examples that worked: amber to orange `[[255,232,70],[255,110,20]]` with stroke `[70,20,0]` and glow `[255,140,0]`; pink `[[255,110,185],[255,45,90]]`; heat `[[255,235,70],[255,70,30]]`; Face ID `[[90,255,240],[40,225,110]]` with glow `[0,255,190]`; lime `[[215,255,70],[90,225,60]]`; new tech `[[90,215,255],[185,95,255]]` with glow `[130,130,255]`. The same options work in `text_behind.py` specs.
- Text: Arial Black (`C:\Windows\Fonts\ariblk.ttf`), thick dark stroke, soft shadow. Width is about 5 x font size for mixed text, so a 1080 px frame fits about 190 px of "YEARS" or 175 px of "iPhone 13".
- Sizes that worked in a 1080x1920 frame: picture cards 260 to 330 px wide; stacked number text 400 px over 150 px; labels 125 to 135 px.

## Text behind the speaker: `text_behind.py`
`text_behind.py <video> <out.mp4> --start-frame N --frames N --spec spec.json`. MediaPipe selfie segmenter (`core/models/selfie_segmenter.tflite`, about 250 KB, downloaded once) gives a person matte; a guided filter sharpens its edge; text is composited between the background and the person; an optional zoom curve is baked in (PCHIP through the given keys). Output is a normal video that replaces the clip's video in the edit (the original audio item stays on the voice track). Request one extra frame (frames + 1) because the Resolve append drops one. About 14 s for 3 s of 1080x1920. Place the text so it is partly hidden by the head or hat and not behind the torso (the jacket hides everything). Edges are decent for head and shoulders; check held objects.

## Sound effects
Folder `<SFX_LIBRARY>`. Loudest moment of each file (seconds from its start): dragon-studio whoosh 0.32, erro 0.23, oxidvideos sfx-2 0.30, oxidvideos sfx-2-whoosh 0.26, oxidvideos whoosh-sound-effect 0.18 (snappy), pop-up-bubble 0.18, sdanezis soft luxury air whoosh 0.69 (strongest, long build, best for a dramatic opening), universfield thin swoosh 1.15. Measure any new sound with an RMS envelope and trim its start so the peak lands on the action (audio source frames are 1/60 s; drop the lead-in with `startFrame`). Short pops used four times in a row must be trimmed or put on separate tracks.

## Colour
Measure, then nudge: take a mid frame of each clip, find the brightest 25% of pixels (a white table or wall should be neutral), compute channel gains `(mean / channel) ** 0.7` clipped to 0.90 to 1.10, apply as CDL slope with power 0.97 and saturation 1.05 to 1.08. Faces: slope 1.03, power 0.97, saturation 1.08, no exposure change when the clip is already around 0.42 to 0.45 mean luma. Verify by measuring the same pixels in a render.

## Voice cleanup
Resolve Voice Isolation (60) and Dialogue Leveler on the voice track, then per-clip gain: measure the speech level of every voice clip from its source (windows above -45 dBFS), target the median (about -18 dBFS), limit to +/-5 dB. Measure the render for the result; sound effects overlapping a clip inflate its reading.

## Transcription for placement
Transcribe the rendered draft with `transcribe.py` to get word times in timeline seconds (see `transcription-and-captions.md`), then place text, pictures and sounds on those times.

## Other tools
- `cut_silence.py`: join clips and remove silence (`--thresh -32 --min-silence 0.45 --pad 0.10`).
- `clean_takes.py`: lists repeated and unfinished lines (suggestion only, the user decides).
- `tl.py`: a timeline engine for assembling clips with silence removal, B-roll, voiceover, undo and FCPXML export. Since the Resolve connection works, the draft is built directly in Resolve; use `tl.py` when working without Resolve or for FCPXML. Commands are listed in its docstring.
- Sound effects: all sounds live in <SFX_LIBRARY> (move new ones there with core/scripts/add_sfx.py; see CLAUDE.md). Currently there: whooshes, pop, erro, cash register ka-ching (dragon-studio-cash-register-kaching-376867.mp3), PlayStation start-up (freesound_community-playstation-turning-on-102458.mp3). Lengths: ka-ching about 1.1 s.
