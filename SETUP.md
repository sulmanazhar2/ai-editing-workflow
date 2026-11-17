# Setup guide (for the AI assistant)

The creator cloned this repo and asked you to set it up. Work through the steps in order. Be short and friendly, ask before downloading or installing anything (say what, from where, size), and do not modify anything outside this folder without a yes.

## 1. Interview the creator (ask in one message, not one by one)
- Channels and what each is about (vlog, gaming, tech, ...), and the languages spoken.
- Which formats: vertical shorts, long-form, live-stream highlights.
- Editor: is DaVinci Resolve Studio installed (the free version has no scripting API; Studio is required for MCP control)? Version?
- Machine: OS, CPU, RAM, GPU (an NVIDIA GPU makes local transcription and NVENC rendering fast), free disk space, where raw footage lives.
- Folders: footage, graphics, sound-effects library, archive for finished videos.
- Style: ask for 2 or 3 reference videos or a short description of the look they want. Do not assume the example style in `workflows/style-vlog-shorts.md`.

## 2. Check the machine
Run local checks (do not guess): Python 3.10 to 3.12 (3.13+ may be too new for some ML packages), Git, Node, FFmpeg (`ffmpeg -hide_banner -encoders` to see whether `hevc_nvenc` exists), `nvidia-smi`, free disk. Report what is missing.

## 3. Install (with permission)
- Python venv inside this folder (`.venv`) with: `faster-whisper`, `torch` (matching CUDA, if any), `opencv-python`, `rapidfuzz`, `Pillow`, `scipy`, `numpy`, `mediapipe`, `rapidocr-onnxruntime` (only if gameplay OCR is wanted).
- FFmpeg on PATH (or note its path in `MY_SETUP.md`).
- DaVinci Resolve MCP: Resolve Studio exposes a scripting API; connect it to your AI through an MCP server for Resolve (the Resolve installer's MCP, or a community "davinci-resolve-mcp"). Enable "External scripting using: Local" in Resolve, Preferences, General. Confirm with a status call that lists the current project.
- Optional audio ears (see `workflows/earshot.md`) in a separate environment.

## 4. Create the working structure
- Create `projects/<category>/` for each channel, `core/scripts/`, and any resource folders the creator named.
- Write `MY_SETUP.md` (machine, paths, channels, languages, tool paths, sound-effects library path). Every workflow file uses placeholders like `<SFX_LIBRARY>`, `<GRAPHICS_DIR>`, `<YOUTUBE_DIR>`, `<GAMING_ARCHIVE>`, `<HOME>`, `<CHANNEL_NAME>`: map them to the creator's real paths in `MY_SETUP.md` and use those.

## 5. Build the basic tools
Read `TOOLS.md` and build the core ones in `core/scripts/` (transcription, silence cutting, render helper, new-project helper) first. Build the rest only when a project needs them. Test each on a tiny clip.

## 6. Smoke test
With a short clip: transcribe, cut silence, open Resolve through MCP, create a project, import the clip, put it on a timeline, render a small file, run a numeric check (`workflows/verification.md`). Report what worked.

## 7. Explain how things work (give the creator this summary, in plain words)
1. Drop raw footage in `projects/<category>/<N>. <name>/raw/`. Raw files are never modified.
2. Tell the assistant which project and what you want. It reads that project's `CLAUDE.md` first.
3. It does the mechanical work locally (transcribe, cut, analyse) and builds a draft on a new Resolve timeline copy. Your own timelines are never touched.
4. You review. Say what to change in plain words; it applies exactly that and writes your correction into the workflow files so it is remembered next time.
5. Only videos to review appear in `export/`; everything else is in `work/`.
6. When a final is approved, the assistant runs the cleanup pass (one final, re-encoded to a sane size, archived).
7. Each session ends with the project's state saved, so the next chat continues where you stopped.

## 8. Personalise
Tell the creator the style file is an example. As they give feedback on the first video, update `workflows/style-vlog-shorts.md` with their own rules. Fill `workflows/packaging-youtube-tiktok.md` with their links when they want titles and descriptions.
