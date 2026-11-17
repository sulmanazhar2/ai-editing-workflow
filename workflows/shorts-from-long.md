# Workflow: vertical shorts from a long-form video

Tool: `core/scripts/shorts_from_long.py <spec.json>` (FFmpeg only, local, GPU encode). Spec format is in the script docstring; a working example is `projects/tech/1. wireless mic/work/s1|s2|s3/spec.json`. Use with `style-vlog-shorts.md` and `verification.md`.

## Method
1. **Find the keepers.** Also run earshot `find` / `timeline` on the audio for loud, laughing, impact moments (`earshot.md`) and combine with the transcript. Transcribe the long video (`transcribe.py`), read the script, pick 2 to 3 self-contained moments of 15 to 30 s (a hook, a demo, a verdict). Offer them to the user in plain words.
2. **Use the raw footage, not the long-form export.** The export has burned-in labels, and the raw takes are higher quality. Ask for or look for the raw folder. Raw takes contain stumbles and retakes; the user's export shows which retake was kept. Recover that with audio matching (`work/align.py`, `align2.py` in the Lark project: slide 1 s windows of the export audio over raw audio, constant offset = one continuous piece). External audio recordings (Recording*.m4a) sync to the video by a constant offset.
3. **Spec the short:** segments (`full` = 9:16 crop around the face with `cx`, or `panels` = stacked comparison strips), `jumpcut` (silence-based cuts, -32 dB, 0.35 s), zoom (`punch` opening, `alt` push on every second cut), labels (NC OFF / NC ON, prices), sound effects, title, end card.
4. **Render.** Pass 1 renders picture and sound; the tool transcribes that render (exact word times) and pass 2 burns captions. Captions: 2 to 3 words, heavy sans, white with one accent colour for key words, name fixes for whisper errors (brand and product names, "base desk").
5. **Verify cheaply:** ffprobe size/fps/duration, loudness (about -14 LUFS, peak under -1.5 dB), transcript of the output (each line once, no stumbles), a strip of 8 small frames. Fix, rerun, repeat.
6. **End card:** if the speaker stops talking, hold the last frame about 1.8 s with "FULL VIDEO / LINK BELOW" (white and accent colour). The user's request: always point to the full video in the link.

## Lessons
- Whisper word end times are stretched; never cut on word gaps. Use silence detection.
- Captions from the source transcript drift after cuts; caption from the rendered output.
- A fixed bug in an old short (a wrong on-screen label) is best fixed by rebuilding from source, not painting over the picture.
- Not built yet: background music, thumbnails, slide/push transition between sections, per-clip face tracking (crop centre `cx` is fixed per segment).

## Gameplay shorts from a finished landscape video (Graves gameplay 2, 2026-11-12): `core/scripts/gaming_shorts.py`
- Source is the user's final export (not raw: item cards and captions are already in it). Layout: blurred zoomed copy as backdrop, 1440x1440 square cut from the 2560x1440 picture (`crop_x` keeps the hero in it) scaled to 1080 in the middle (y 470), channel pill <CHANNEL_NAME> + a two-line hook at the top, word-pop captions below the panel (y 1700), last 1.8 s held with FULL VIDEO / LINK BELOW. Audio loudnorm -14 (integrated reads -15 to -17 because of the end hold), peak -1.3 dBFS. Captions come from the transcript of pass 1 (exact times); profanity is masked (F***, B***H).
- Picking moments: transcribe the final video, run earshot `find` (explosion/gunshots, screaming, impact) and loudness per second, then choose by reading the transcript (sentence start to payoff, 37 to 55 s). The automatic score alone picked weak windows (silence-heavy); the transcript read decided. Spec example: `projects/gaming/3. Graves gameplay 2/work/shorts_spec.json`.
- Hook text only restates what the user said (83,000 damage, "run boys run", "get that boy"). Do not invent claims.
- **User feedback on the Graves shorts (2026-11-12):** liked Short 1 (it concludes the game: most damage, 83,000) and Short 4; Short 2, 3 and 5 were not good enough and were deleted. **No dead seconds at the end**: the 1.8 s held end frame ("FULL VIDEO / LINK BELOW") was removed from gaming shorts; the tail is cut about 0.4 to 1 s after the last spoken word. A short that wraps up the story or payoff beats a pure action window. The user gives the next clip timestamps themselves.
- **End text without dead time (user, 2026-11-12, triple kill short):** the user likes the "FULL VIDEO / LINK BELOW" end text but not a frozen frame. `gaming_shorts.py` option `"end_over": 2.0` shows it over the last 2 s of live footage (no extra length). The user gives exact start and end times for clips they already know are good (`trim_tail: false` keeps their end).
