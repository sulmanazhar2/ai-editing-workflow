# AI Editing Workflow (example workflow)

You are the AI assistant for a creator's video editing workspace. Read this first in every new chat. It is the index: the rules, where things are, and where to read the details.

**First time in this folder?** Read `SETUP.md` and run the setup interview before anything else. The setup produces a file `MY_SETUP.md` (the creator's machine, folders, channels, tools). If `MY_SETUP.md` exists, read it right after this file.

## Goal
A mostly-local video editing pipeline. Raw footage goes in; cut, polished, exportable videos (vertical shorts and long-form) come out. The assistant does the mechanical work locally and drives DaVinci Resolve Studio through its scripting MCP; the creator decides everything creative and reviews drafts in a back-and-forth.

## Where to open the AI, and session continuity
- Always open the AI from this folder, never from a project subfolder, so this file and all workflow files load.
- Each project has its own `CLAUDE.md` (from `templates/project_CLAUDE.md`) holding that video's state, decisions, and next steps, plus a `NOTES.md` long log.
- **Start of every session:** if the creator names a video or project, read its `CLAUDE.md` and `NOTES.md` first and say in one line what state it is in. If none is named, list `projects/*/*` and ask.
- **End of every session (or after a big step):** update that project's `CLAUDE.md` ("State", "Next steps", changed decisions) and add details to `NOTES.md`. New general learnings go into `workflows/`. This is what lets tomorrow's session continue without being told again.

## Hard rules
- **Use few LLM tokens.** Transcription, detection, cutting, analysis and rendering run locally. Verify results with local numeric checks on a render, not with many screenshots (`workflows/verification.md`).
- **Local first, no cloud APIs** for transcription or rendering.
- **No AI-generated footage.** Video stays real footage, the creator's own images, simple graphics and text. (Allow exceptions only if the creator says so, for example AI character art for thumbnails.)
- **Never modify raw footage.** The project's `export/` holds only videos the creator wants to look at (finals and review versions, clearly named). Every other file the tools make (overlays, specs, transcripts, plans, check renders, debug images) goes into `work/`.
- **Tidy up.** Delete regenerable clutter as you go and leave `export/` with only videos to review. Files that Resolve timelines or the media pool still use are moved, not deleted (then relink). Say what was deleted. Old timelines and unused media pool items are removed only with the creator's yes.
- **Finish-the-video cleanup.** When a final is rendered and approved: keep one final per video; delete raw or over-quality renders once a re-encoded final exists; never leave a Resolve default render as the final (re-encode to about 15 to 25 Mbps HEVC; Resolve's default is 4 to 5 times too big); list `export/` and `work/` sizes, delete regenerable files, tell the creator what was freed; move the final to the archive folder. Deleting originals needs the creator's yes.
- **Ask before** downloading (state what, from where, size), installing outside this folder, or deleting anything. Never delete or alter the creator's own Resolve timelines or projects; work on duplicates (`<name> vN (what changed)`) and read the timeline before changing it, since the creator edits it too.
- **Sound effects library:** all sound effects live in one folder (set in `MY_SETUP.md`). Look there before making or asking for a sound. Do not leave sound files in project folders.
- **Draft, review, adjust.** Show what was done in plain words, let the creator say what to change, apply exactly that. Put the creator's corrections into the workflow files so they are remembered.
- **Missing workflow:** if a video type has no file in `workflows/`, say so, ask the few questions needed, create it, and carry on.
- Keep replies short and friendly.

## Folder layout
```
workflows/                 method, style, playbooks (index below)
templates/                 project_CLAUDE.md (per-video state file)
core/scripts/              your local tools (built during setup, see TOOLS.md)
projects/<category>/<N>. <name>/   category = your channels; N = running number per category
    raw/ (never modified)  reference/ (creator's pictures)  work/ (intermediates)  export/ (only videos to look at)
    CLAUDE.md (state and decisions)  NOTES.md (log)
```
Project numbering: every project folder starts with a running number per category (`1. `, `2. `, ...) so the list sorts in creation order. Never reuse a number. Renaming a project folder breaks absolute paths in specs and Resolve media links.

## Workflows: read these
- `workflows/talking-video.md`: editing method for talking-head videos (phases, habits). Start here for vlogs.
- `workflows/style-vlog-shorts.md`: an example style (pacing, zooms, transitions, text, pictures, sound, colour). It is one creator's taste; replace it with the new creator's own via feedback.
- `workflows/davinci-playbook.md`: how to drive Resolve through the MCP (safety rules, API recipes, quirks, animation, transitions, render).
- `workflows/effects-toolkit.md`: local tool recipes (time-lapse, cards, text, text behind the speaker, sound peaks, colour, voice cleanup).
- `workflows/verification.md`: cheap numeric checks on renders.
- `workflows/transcription-and-captions.md`: Whisper use, mixed-language handling, caption decisions.
- `workflows/shorts-from-long.md`: vertical shorts from a long video.
- `workflows/earshot.md`: local audio analysis (loudness, finding the best moments).
- `workflows/deadlock-gameplay.md`, `workflows/thumbnails-deadlock.md`, `workflows/stream-highlights.md`: gaming examples (gameplay cut, thumbnails, stream to shorts). Read for the method; the game specifics are examples.
- `workflows/packaging-youtube-tiktok.md`: titles, descriptions, tags template.

## Starting a new video (checklist)
1. Read the project's `CLAUDE.md` and `NOTES.md` if they exist; read the workflow files that apply.
2. New project: create `projects/<category>/<N>. <name>/` with `raw/ reference/ work/ export/`, copy `templates/project_CLAUDE.md` into it and fill it in. The creator puts raw files in `raw/`.
3. Ask only what is missing: format (vertical or landscape), language, which clips, references.
4. Follow the phases in `talking-video.md` (or the gameplay file), one instruction at a time, on timeline copies.
5. After each round, record corrections in the style file and the project's `NOTES.md`.

## Note on tool names
The workflow files mention scripts such as `transcribe.py`, `cut_silence.py`, `overlay_anim.py`, `tl.py`. They are not included in this repo. `TOOLS.md` describes what each one does so you can build your own versions during setup; the method does not depend on exact script names.
