# Stream highlights to shorts (playbook, made 2026-11-16/17)

Goal: the user says "the stream recording is ready, make shorts" and we produce finished vertical shorts (YouTube Shorts, TikTok, Instagram) of funny/exciting moments. First proven on the GTA 5 stream of 2026-11-16 (9 shorts, user: "looks good").

## Where things go (user rule)
- Outputs and working files go in the **stream's own folder**, not in the AI Editing Workflow folder: `<stream folder>/shorts/` (the shorts, plus `shorts/tool_tests/` for tool experiments) and `<stream folder>/shorts_work/` (audio, transcripts, `spec.json`, `.ass` files). Scripts stay in `core/scripts/`.
- Cleanup after the user has picked: delete big temp files (cut excerpts, extra wavs), keep the shorts and `spec.json`.

## The method that works (use this)
1. **Check the audio tracks**: `python core\scripts\stream_prep.py "<stream.mp4>" "<stream folder>\shorts_work"` lists the tracks with levels. OBS multitrack order on 2026-11-16: 0 = mix, 1 = game audio, 2 = mic, 3 to 5 silent. Confirm each new stream (mic = long silences with speech peaks).
2. **Prepare**: `... stream_prep.py <stream> <work> --mic 2 --game 1` extracts `a_mic.wav` and `a_game.wav`, transcribes both locally (large-v3, about 6 minutes per track for 3.5 h on the GPU; mic transcript is `t_mic.json`, game dialogue `t_game.json`) and writes `candidates.txt` (top 25 windows with the mic transcript).
3. **Pick moments by reading** `candidates.txt` and the mic transcript (grep `t_mic.json`). Look for: a reaction ("oh my god", "what the f", "I'm fine"), a fail or twist, a running joke, a setup and a payoff. Look at a contact sheet of frames for the candidates (a few frames per 5 s, tiled) to confirm what happened on screen. Whisper word times are loose in long silences, so check cuts against frames or mic loudness.
4. **Write the story**: each short joins **2 to 5 moments** (callbacks and themes work best, for example four times "can I kill him? no"), max 30 s, usually 15 to 28 s, hook on top (white line + yellow line, 2 to 4 words each, plain and funny).
5. **Build**: write `spec.json` (format in the docstring of `core/scripts/stream_shorts.py`), then `python core\scripts\stream_shorts.py <spec.json> [names]`. `segs` = hand-picked parts, `auto` = a range with the dead air cut out of the mic track. Run with `PYTHONUTF8=1`.
6. **Check** (cheap): duration, loudness about -14 LUFS (`ebur128`), 2 or 3 frames per short. Show the list with one line each, let the user judge, fix exactly what they say.

## Look of the shorts (approved by the user 2026-11-17)
- 1080x1920. Top: **social tag with the YouTube, Twitch and Kick icons and `<CHANNEL_NAME>`** (`core/assets/social_icons/tag_<channel_name>.png`, built by `core/scripts/make_social_tag.py` from the user's icons in `core/assets/social_icons/user/`). **Always use the icons, never plain text for socials.** Username `<channel_name>` on Twitch, YouTube and Kick (TikTok has a different one, add only if asked).
- Then hook text, facecam panel, gameplay panel (4:3 crop that drops the baked-in overlays), word-pop captions (current word yellow, profanity masked F***), whoosh on every cut, error sound on fail moments, loudness -14 LUFS.
- The stream layout is baked into the recording: facecam inner area x28 y345 434x232 in 1920x1080, chat and recent-follower bars below it on the left; gameplay is x480 to 1920. If the OBS scene changes, check one frame and change `cam_crop` / `game_crop` in the spec.
- Not done yet: punch-in zooms on the action, a bigger SFX set (the library has only whooshes and `erro.mp3`), a user voiceover for a story, a silent-action finder with `earshot find` (explosions, crashes) for moments without talking.

## Free tools we tested (2026-11-16, in `tools/clippers/`; user decision pending on removing them)
- All need the **mic track only** (the mixed track has game dialogue and the LLM returns nothing). They also needed `pip install "av<16"` (PyAV 19 breaks faster-whisper); AutoClip needs `providers.ollama.model` in `~/.autoclip/config.json` and `PYTHONUTF8=1`.
- AutoClip (MIT): good moment picks, but centre-crop without facecam, one raw clip per short, 38 Mbps files.
- Steezy clipper: same moments as ours but only 2 clips, blurry zoomed facecam. Headless: `pipeline.process(mic_only_video, Config(model="qwen3:14b", whisper_model="small.en", layout="stream"))`.
- Clips Kitty: command line needs a URL; not run.
- Verdict: our own builder wins (story shorts from several moments, facecam, socials). The tools are only a possible second opinion for finding moments. Ollama (qwen3:14b 9.3 GB, gemma3:12b 8.2 GB, in `<HOME>\.ollama`) and the three folders can be removed with the user's yes.

## Log
- 2026-11-16: GTA 5 stream (3.5 h, `<YOUTUBE_DIR>\Stream recordings`): 9 shorts made (best driver, cops dead end, great driving day, can I kill him, Chop the dog, race not over, fun like this, truck gone, NPCs vs me). User feedback: good; wanted socials with icons (done), and documentation so any stream can be processed (this file, `stream_prep.py`, `stream_shorts.py`).
