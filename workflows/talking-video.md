# Workflow: talking video (vlog, tech; shorts and long-form)

Use with `style-vlog-shorts.md` (look and feel), `davinci-playbook.md` (how to drive DaVinci Resolve), `effects-toolkit.md` (local tools for graphics and sound) and `verification.md` (how to check results cheaply).

Input: raw clips of the user talking to camera (Urdu/English mixed or English only), extra footage for B-roll, voiceover recorded later. Project folder: `projects/<category>/<N>. <name>/{raw,reference,export}` (`NOTES.md` in the project holds that video's own details; this file holds only the method).

## Principle
Conversational editing, one instruction at a time, on copies of the timeline. The assistant does the fast mechanical work locally and shows results; the user makes every creative decision. Fully automatic cleanup was tried (take grouping) and the user judged the draft poor, so it stays an optional helper that only lists suggestions.

## Phases
1. **Assemble.** Join raw clips in order and remove only silence (`cut_silence.py`, `tl.py add --silence`). Transcribe locally (`transcribe.py`, see `transcription-and-captions.md`) so lines can be shown with timestamps.
2. **Rough cut by instruction.** The user watches and says what to cut or keep (time ranges, "keep the last take", "this part here"). Apply it. If asked, `clean_takes.py` lists repeated and unfinished lines (suggestion only). Several complete takes: list them, let the user choose.
3. **Voiceover and B-roll.** The user records voiceover phrase by phrase (in Resolve). Then place B-roll clip by clip against each phrase using the user's timestamps (details in `style-vlog-shorts.md`). Long actions become time-lapses (pre-rendered), short key actions stay real-time; B-roll audio is muted. The timeline is now a complete draft.
4. **Review the draft.** Render a preview, user reviews, gives notes ("remove 0:14 to 0:16", "show this here"). Apply. Repeat until the story is complete.
5. **Polish layers** (user decides which): zoom pattern and opening punch, transitions, picture inserts, text and labels, text behind the speaker, sound effects. Each in a new timeline copy with a short name (`v5 (feedback draft)`), applying feedback exactly as given.
6. **Finish:** colour (subtle CDL), voice cleanup and level matching, music (when asked), thumbnail, final export. Check with local numeric tests (`verification.md`).
7. **Record** what was learned in the project's `NOTES.md` and, when it generalises, in these workflow files.

## Session continuity
- Open Claude Code from the workspace root (`<YOUTUBE_DIR>\AI Editing Workflow`). Each project has its own `CLAUDE.md` (state, decisions, files, next steps) plus `NOTES.md` (log). At the start of a session read them for the project the user names; at the end update them. See the root `CLAUDE.md`.
- Create a project with `core/scripts/new_project.py`, which also creates the `CLAUDE.md` from `core/templates/project_CLAUDE.md`.

## Habits that worked
- **Read the timeline before changing it.** The user edits the same project at the same time (trims, moves, adds clips). Work on a duplicate named `<project> vN (what changed)`; never overwrite the user's own timeline. Keep older versions for comparison; clean-up of old versions only with the user's yes.
- **Times the user gives are DaVinci timecodes** (seconds:frames at the timeline frame rate), for example 11:23 = 11 s + 23 frames = 11.77 s at 30 fps. Check them against the transcript before placing anything.
- Transcribe the rendered draft (through Resolve) to get exact word times on the final timeline for placing text, pictures and sounds.
- Ask for what is missing (a picture, a clip, a line to record) in one short list, and say what each item is for.
- Show the user what changed in plain words after each round; keep answers short.
- Save the user's corrections in `style-vlog-shorts.md` so they do not have to repeat them.

## Variants
- Talking to camera, voiceover over footage, a mix of both, daily vlog, graphics sections: same timeline, different layers. If the video type is new (for example a long-form tutorial), say so and create a matching workflow file after asking the few questions needed.
- Shorts from a long video: see `shorts-from-long.md` (tool `shorts_from_long.py`).
