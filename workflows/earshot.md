# earshot: ears for the assistant (audio analysis, moment finding)

Installed 2026-11-11 (user decision). Repo: github.com/Leonxlnx/earshot (MIT). Local, CPU only, no cloud, no API key needed.

## Where and how to run
- Own environment: `tools\earshot-env` (not the main `.venv`). Models (about 1.1 GB: CLAP + AudioSet AST) cached in `<HOME>\.cache\earshot`.
- Run: `tools\earshot-env\Scripts\earshot.exe <command> ...` from the workspace folder. ffmpeg is found if it is on PATH; if not, prepend the ffmpeg `bin` folder (see `core/scripts/common.py`) to PATH for the call.
- Each CLI call reloads the models (20 to 40 s). For many calls in one session, start the MCP server instead (`earshot mcp`, models stay warm); not wired into Claude yet.
- Health check: `earshot doctor`. Write results (`--out`) into the project's `work/`, never `export/`.

## Commands we use
- `measure <file> [--light] [--start S --end E]`: LUFS, true peak, LRA, envelope, spectrum. No models, fast. Use as the final loudness check on shorts (target about -14 LUFS, true peak under -1.5 dB).
- `find <file> "<query>" [--top 5] [--kind event|texture]`: the best matching moments in a long recording by text query (for example "loud laughter", "explosion", "crowd cheering", "gunshot"). Candidate finder for high-impact moments; use on the audio of gameplay and vlog takes.
- `timeline <video> [--cues 1.5,3.2,...] [--detail low|normal|high]`: sound events with time, level and tag, picture cuts, beat grid, and whether effects land on cuts or cue frames. Use to check that SFX hit on the cut or word, not late.
- `critique <video> [--fast]`: scorecard against 7 launch films with timestamped fixes. It reflects launch-film taste (tech promos), not vlogs or gaming; treat as hints only.
- `describe <file>`: what a sound is, peak, attack time. Useful when judging a new sound effect before adding it to the library.

## Not used
- `forge`, `sfx`, `kit` (synthesize sounds in code). Our rule is real recorded effects, so these stay unused.
- `llm` (needs a Gemini key and goes to the cloud). Never.

## Using it for high-impact moments (shorts from a finished video)
The earlier moment finder gave results the user did not like. earshot is the replacement signal, not the final judge:
1. Run `find` with several queries suited to the video (laughter, shouting, impact, music hit, silence-then-burst) on the final export's audio; also `timeline --detail high` for event density and loudness peaks.
2. Combine with the transcript (`transcribe.py`): a good short starts at a sentence start and ends at a sentence end, 15 to 40 s.
3. Offer a shortlist (3 to 6 candidates, time range and one plain line each, plus why). The user picks; apply their taste in `style-vlog-shorts.md`.
4. Record in this file what worked and what did not (see log below) so the method improves.

## Limits (from the README)
- Cannot judge taste, emotion or speech content. Tags inside a busy mix are hints only. Content above 14 kHz is ignored.
- Its own tests were small; do not trust a score without listening or a numeric cross-check (`verification.md`).

## Log
- 2026-11-11: installed, `earshot doctor` healthy (CPU, torch 2.15). Not yet tried on a real video. First test: run `find` and `timeline` on a final export the user knows the best moments of, and compare with the user's picks.
- 2026-11-12: Graves2 test: earshot find (scream/explosion/gunshot, 4 s windows) works on 37 min mono wav in about 1 min per query; hits useful only with the transcript to know what was said.
- 2026-11-12 (Graves gameplay 2, 37 min gameplay audio): `find` with "loud explosion", "a person screaming excitedly", "loud gunshots and impacts" (`--win 4` or 5, `--top 12`) took about a minute per query on CPU and returned plausible loud-fight windows, but many had no speech and on their own they picked weak short windows. Best use: candidate list merged with the transcript and per-second loudness, then a human read of the transcript. `timeline` on the final video reported loudness (-24 LUFS final, so shorts need loudnorm to -14) and is slow-ish; the "music section" tags on game audio are noise.
