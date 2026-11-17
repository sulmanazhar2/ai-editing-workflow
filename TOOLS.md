# Tools to build (specs)

The scripts are not included. These are the jobs the workflow files expect; build simple versions in `core/scripts/` with your AI. All run locally and are called from the project's venv. Names are suggestions.

| Tool | Does |
|---|---|
| `transcribe.py <media> <out.json> [--model large-v3] [--language en\|ur\|auto]` | Local GPU transcription (faster-whisper) with word-level times. |
| `cut_silence.py <out.mp4> <in...> [--preview] [--thresh -32] [--min-silence 0.45] [--pad 0.10]` | Join clips and remove silence; preview mode lists the cuts without rendering. |
| `clean_takes.py <work_subfolder> <clips...>` | From transcripts, list repeated and unfinished lines so the creator picks the best take (suggestion only). |
| `overlay_anim.py <spec.json> <out.mov>` | Transparent animated overlays (cards, text, labels) with motion blur, as ProRes 4444 with alpha, placed in Resolve. |
| `caption_overlay.py <spec.json>` | Animated word-pop captions as a transparent overlay. |
| `text_behind.py <video> <out.mp4> ...` | Text behind the speaker (person segmentation with MediaPipe selfie segmenter, text layer between background and person). |
| `render.py` / `common.py` | Shared helpers (paths, FFmpeg wrapper, NVENC settings). |
| `reencode_archive.py <files...> [--cq 24] [--maxrate 30M]` | Shrink huge exports to HEVC NVENC, write next to the original. |
| `new_project.py <category> <name>` | Create the numbered project folder, subfolders, and a filled-in `CLAUDE.md` and `NOTES.md` from the template. |
| `add_sfx.py [--copy] <files...>` | Move new sound effects into the shared library, never overwriting. |
| `stream_prep.py`, `stream_shorts.py` | Stream recording to candidate moments and finished story shorts (see `workflows/stream-highlights.md`). |
| game-specific detectors | For example OCR of purchased items in a gameplay recording (see `workflows/deadlock-gameplay.md`). Build only for games you edit. |

Build order: transcribe, cut_silence, render helper, new_project, then the rest as projects demand. After building a tool, add its exact usage line to your `MY_SETUP.md`.
