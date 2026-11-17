# AI Editing Workflow (example)

A way of editing videos with an AI assistant (Claude Code) that does the mechanical work locally and drives DaVinci Resolve Studio through its scripting MCP, while you make the creative decisions and review drafts.

This repo is **instructions only**: the method, rules, style notes and Resolve recipes. It has no projects, footage, or scripts. The workflows were written from real editing sessions (vlog shorts, gameplay videos, stream highlights) and are meant as a starting point you adapt.

## Quick start
1. Install [Claude Code](https://claude.com/claude-code) (or another coding AI that reads `CLAUDE.md`), Git, and DaVinci Resolve Studio.
2. Clone this repo and open your AI inside the folder:
   ```
   git clone <this repo URL> ai-editing-workflow
   cd ai-editing-workflow
   claude
   ```
3. Say: **"Read CLAUDE.md and SETUP.md and set everything up for me."** The AI interviews you, checks your machine, installs tools (asking first), builds the helper scripts, runs a smoke test, and explains how to work.

## What is inside
- `CLAUDE.md`: the index and hard rules the AI follows in every session.
- `SETUP.md`: step-by-step setup for the AI to run.
- `TOOLS.md`: specs of the local tools to build.
- `workflows/`: editing method, style, Resolve playbook, effects, verification, transcription, shorts, gaming examples, packaging template.
- `templates/project_CLAUDE.md`: per-video state file.

## Core ideas
- **Local first.** Transcribe, cut, analyse, render on your own machine. Few LLM tokens; verify with numbers, not screenshots.
- **Never touch raw footage or your own timelines.** Work on copies.
- **Draft, review, adjust.** The AI drafts, you correct, corrections are saved in the workflow files.
- **Memory by files.** Each project keeps its state in `CLAUDE.md`, so a new chat continues where the last stopped.
- **Tidy.** `export/` only has videos to look at; everything else is in `work/`.

## Notes
- The style file and gaming files are one creator's examples. Replace the taste with yours as you give feedback.
- Placeholders like `<SFX_LIBRARY>` or `<CHANNEL_NAME>` are mapped to your paths during setup.
- Needs DaVinci Resolve **Studio** for scripting. An NVIDIA GPU is recommended for local transcription and fast encoding.

## License
MIT, see `LICENSE`.
