# Workflow: Deadlock gameplay (long-form and shorts)

Input: a raw recording (stream recordings can be about 4 hours; 2560x1440, 60 fps, h264, mkv). The user says which time range is the game to use, for example "from 1:00:00 to 2:00:00". Project: `projects/gaming/<name>/{raw,reference,export}`. Game tools: `games/deadlock/`. For the editing method, look and sound use the shared files: `talking-video.md` (method), `style-vlog-shorts.md` (look and feel, adapt for gameplay), `davinci-playbook.md`, `effects-toolkit.md`, `verification.md`. New games (GTA next) get their own file of this kind.

## Recording facts
- OBS recordings have 6 audio tracks that are all identical (a settings mistake): use track 1, mic and game audio cannot be separated. Fixing OBS to record mic and game audio on separate tracks would improve impact detection a lot.
- Gaming videos are landscape 1920x1080 or 2560x1440 at 60 fps for long-form; shorts are cut to vertical 1080x1920 (30 fps timeline unless the user says otherwise).

## Steps
1. **Cut the range** the user names out of the recording (FFmpeg, no re-encode for analysis copies where possible).
2. **Remove dead time** (to build): when the user is dead or spectating, menus, long silent stretches. Detect from on-screen state (respawn timer, spectator HUD); confirm on real footage first.
3. **Detect item purchases** (`games/deadlock/detect_purchases.py <video> <out.json> [--start S --end E --fps 3]`):
   - Reads the "ITEM PURCHASED <name> $cost" popup on the right side (about x 82 to 100 %, y 34 to 50 % of the frame), which appears about 1 s after the purchase; an item tooltip at the top left also names it. Two items bought together show both.
   - RapidOCR on the popup crop, fuzzy match (rapidfuzz) to item images in `<GRAPHICS_DIR>\Deadlock abilities` (116 PNGs, some file names have typos, there is a `Corrupted\` folder). About 12 minutes for 27 minutes of 1440p at 3 fps (CPU OCR is the bottleneck; speed up by OCRing only when the popup region changes).
   - Unknown or garbled text is dropped, never guessed. Known items = local images plus the list at deadlock.wiki/items (cache it locally once). If a wiki item has no local image, tell the user.
   - Tested on a 27 minute raw recording: most hits correct; false hits seen: "mmmmm", "120181", "PL3F", a garbled "ITEM PURCHASED" with no item, and "Siphon Life" (not an item). The user reviews the list and removes wrong entries.
   - Edited videos with banners over the popup are poor test material; use raw recordings.
4. **Add overlays**: the item picture and name at each purchase, built as small cards with `overlay_anim.py` (see `effects-toolkit.md`), placed on a video track above the gameplay. Not built yet.
5. **Suggest clips for shorts** (high-impact moments, to build): from audio loudness spikes and screen activity, output a ranked list of timestamps with short preview clips as drafts. The user picks, reorders and says how to combine them; they can also give timestamps by hand. Plain loudness cannot tell a kill from other loud moments (kills were only 2 to 3 times louder than their surroundings), so a sound-match approach is needed for kills, deaths and parries (postponed).
6. **Draft and review:** preview, list of cuts and overlays, notes from the user, apply, repeat (same loop as `talking-video.md`).
7. **Final render** with NVENC; shorts reframed to vertical, polish layers as in `style-vlog-shorts.md`; captions only if asked (Roman Urdu for Urdu words).

## Reference timestamps (for building the kill/death/parry detectors later)
From one edited video: kills 4:04, 4:31, 9:26, 10:47, 12:24, 13:44, 14:33, 15:48, 18:45, 19:53; deaths 15:57 and 17:23; parry 19:52; purchases 6:57 Improved Spirit, 8:59 Spirit Snatch, 12:51 Veil Walker, 15:15 Tank Buster and Boundless Spirit. Several kills happen in busy fights where the sound cue may be masked.

## Notes
- First pass decision: item purchases and "high impact moment" suggestions only; kills, deaths, parries later.
- Thumbnails: see `thumbnails-deadlock.md` (the user's style learned from their folders in `<GAMING_ARCHIVE>`; made locally from the user's/wiki cut-outs and text; ask a few questions, make 3 drafts, the user picks).
- Record every new preference from the user here.

## Rules from the user (2026-11-10, Graves video)
- **Whenever dead time or any section is cut out and two pieces are joined, put a Cross Dissolve between them** (about 20 to 60 frames at 60 fps, as the user does in their edit). No hard cuts at death cuts.
- **Death handling:** the user's edit keeps the death (about 3 s) then dissolves to the next fight, skipping the respawn and the run back. Reading that timeline: cut points are source times where the user died (e.g. edited 8:50 and 14:08).
- **Items bought while dead:** the user often buys items during the respawn timer. The purchase popup is on the raw footage, so detect purchases on the RAW file and map each to the edited timeline; a purchase inside a removed stretch is shown right after the dissolve (the first moment of the next kept piece). Cross-check with the bottom-left item bar before and after the cut.
- **Item overlays:** picture (and name) of every purchased item fades/slides in smoothly and fades out smoothly; two items bought together are shown together (place them where they do not cover the HUD).
- **Captions for loud moments:** on loud or screaming moments add short engaging text with a funny sound effect; make the assets from what the user says and how.
- **Trimmed version:** also offer a second timeline that removes inactive parts (farming, clearing waves, walking) and keeps the action. Always as a new timeline; the user's own timeline is never edited.
- **Sound effects feedback (Graves v2):** the user removed the air-horn and sad-trombone sounds as "extreme" and kept boom, bonk and scratch. Use fewer, subtler effects; synthesized effects live in the project's `work/assets/`. The user edits the timeline while I work: re-read it before every change.
- **Item card look (user feedback 2026-11-11):** no white border or rounded frame with padding (the transparent wiki pictures looked cut up); auto-crop the picture to its visible area, big (about 380 px wide in 2560x1440), soft shadow only. One item sits on the LEFT edge, a second item bought at the same time on the RIGHT edge (never centre). Card clips are small canvases (540x780) placed with Pan +-2500 and Zoom 0.5417 (Pan units are not pixels: about 0.387 px per unit at that zoom). Rendering full 2560x1440 overlays is 20x slower; use small canvases.
- **Item timing:** the popup time is not always the purchase time. The user corrected: Restorative Shot +3 s later, Extra Stamina +4 s later, Stamina Mastery 3 s earlier. Always let the user confirm item names; the OCR gave false hits (Escalating Resilience was Grit, Sprint Boots, Torment Pulse and Rapid Recharge were not bought). Items without a local picture (Knockdown, Spirit Shielding) get a plain text card until the user supplies the picture.

## Lifecycle of the item overlays (approved by the user 2026-11-11, "this looks awesome")
1. **Detect on the RAW recording** (`detect_purchases.py <raw> out.json --start S --end E --fps 2`; about 15 min for 46 min of 1440p; run it in the background at the start). Drop `None` hits. Show the list to the user and apply their corrections (names, timing offsets of a few seconds, extra items bought together that the popup missed).
2. **Pictures come from the user's folder `<GRAPHICS_DIR>\Deadlock abilities` or from the wiki, NEVER made up.** If an item has no picture, get it from `https://deadlock.wiki/<Item_Name>` (ask before downloading, say what and from where) or ask the user; do not draw a stand-in card (the user explicitly rejected that). Items bought while dead often show no popup: ask the user or compare the item bar before and after the death.
3. **Cards:** auto-crop the picture to its visible area (alpha > 230, trim 3 px), no frame, soft shadow, 380 px wide in 2560x1440, small canvas 540x780 at 60 fps, 220 frames (3.7 s), slide in from the screen edge and slide out; render with `overlay_anim.py` (6 in parallel, about 100 s for 19 cards).
4. **Place in Resolve:** import the clips, add on V3 (left) and V4 (right), `Pan` -2500 / +2500, `Zoom` 0.5417, short ding on an audio track; map the raw purchase time to the timeline item that contains it; a purchase inside a cut stretch shows right after the dissolve. Re-read the timeline first; replace a wrong card with `MediaPoolItem.ReplaceClip(newpath)`.
5. Check one frame with `get_image` at the moment two cards are up.

## Trimming "inactive" parts: feedback (Graves v3)
- The user was happy with the **death/respawn cuts** (cut the respawn, keep about 3 s of the death, cross dissolve).
- The user did NOT like the audio-loudness based removal of "farming / walking" parts (the automatic trim in v3). It cut too much or the wrong things. Do not repeat that method unasked. Next time: either let the user mark the parts to remove (they give timestamps or edit a duplicate and I read their cuts), or try a different method (for example only remove long stretches with no fight at all and ask about each). Offer the trimmed timeline as a suggestion only.
- The user trimmed the end of the trimmed timeline themselves; read their timeline before rendering.

## Shorts from a gameplay video
- At the end of a gameplay video also output **two vertical shorts** from the most action-packed stretches (about 45 to 55 s each). Find them from loud, dynamic audio (top 30 loud seconds of a 50 s window plus variation) outside death time, then look at one contact sheet to reject talk-only or running-only windows. Build them as vertical 1080x1920 timelines in Resolve (gameplay fitted to the width on top of a zoomed, blurred copy) and render drafts into `export/`.

## Lessons from "Graves gameplay 2" (user feedback 2026-11-12)
- **Raw time is not timeline time.** A Resolve timeline built from a raw file can start a little into the file (here V1 began at source frame 116, 1.93 s). Never place at `start + raw*fps`; read the V1 items (`GetLeftOffset()`, `GetStart()`) and map each raw frame through the piece that contains it: `timeline_frame = item.GetStart() + (raw_frame - item.GetLeftOffset())`. My first v2 put every card and caption about 2 s late; the user pushed them all back by hand. After the user cuts, map again through the new pieces.
- **The popup shows about 0.5 to 1.5 s after the purchase** (checked frames: Grit, Weapon Shielding, Spirit Lifesteal). Put the card at popup time minus about 1 s, or at the time the user names (they name timeline times: use them as given).
- **The OCR detector misses items**: Grit (popup visible for 3 s, not detected), Weapon Shielding, Stamina Mastery (shop opened, no popup), and items bought while dead. Run again at a higher fps or read the item bar change; always ask the user for missed ones.
- **"ITEM SOLD" is not a purchase.** A sold item (Restorative Shot) was read as a purchase, and the real purchase (Ricochet) was stacked right under it. Read the popup title (PURCHASED or SOLD) and the second stacked entry.
- **Only the user's own purchases count.** Check the hero: top bar, left team, second portrait (Graves, white hair) has a highlighted shade behind it = the user's hero. When the user is dead the name/portrait shows a red respawn countdown; purchases then (Express Shot) are the teammates', ignore them. To build: a detector for this (alive flag per second) so purchases while dead are dropped.
- **Card height:** the canvas is 540x780, so a card taller than about 700 px at 380 px wide is cropped (Capacitor, 936 px). Shrink the width so the height is at most 700 (`build2.py` in the project work folder).
- **Captions:** Whisper word times are not exact; the user moved "Get that boy" later by ear. Snap a caption to the loudest onset of the spoken word in the audio wave (`ffmpeg` envelope or earshot `find`) rather than trusting the word timestamp. The user removed weak captions ("I think we won", "There you go", "Free Walker") and cares more about impact than quantity: fewer, stronger captions.
- **Resolve API:** the clip scale properties are `ZoomX`/`ZoomY` (a `Zoom` key is silently ignored). When the user edits a timeline in place, read it, then `DuplicateTimeline` and work on the copy (v3).
- **Re-map after every user edit (Graves 2, 2026-11-12):** the user kept cutting v3 while I worked. A time they gave in the timeline earlier no longer points at the same moment after a new cut. Convert their time to a source (raw) frame with the pieces as they were, then map to the current pieces. Re-read V1 right before placing anything, and check one frame afterwards.
- **Missing item pictures from the wiki (user approved the download, 2026-11-12):** the item cards are screenshots of the wiki tooltip box (deadlock.wiki/<Item_Name>, right-hand box, without the "Development name" footer). Make one with headless Edge: `msedge --headless=new --force-device-scale-factor=2 --window-size=1400,1000 --screenshot=page.png <url>`, crop the tooltip box (about x 934-1280, y 265-780 at 1x, then cut the footer), save as `<Item Name>.png` in `<GRAPHICS_DIR>\Deadlock abilities` (user rule: new pictures go there too). Weapon Shielding was added this way.
- **Render:** Resolve's H264_NVIDIA default is about 120 Mbps and `VideoQuality` is rejected by the API. Render to the project `work/render_big/`, re-encode with ffmpeg NVENC (about 25 Mbps, loudnorm optional) into `export/`, then delete the big file.

## Whole pipeline that worked: Graves gameplay 2 (2026-11-11/12, user: "a good success", use this every time)
Order, with the tool for each step:
1. **Project + Resolve:** `new_project.py gaming "<name>"`; Resolve project at the recording's size/fps (2560x1440, 60); `CreateTimelineFromClips` for a raw timeline. Raw stays where it is (note the path in the project CLAUDE.md).
2. **Items:** `games/deadlock/detect_purchases.py` on the RAW (about 12 min for 37 min at 2 fps, run it in the background first); `transcribe.py` and the extracted audio in parallel. Cards via the build script (copy `work/build.py`/`build2.py` from project 3), place on V2 (left) / V3 (right) with ZoomX/ZoomY 0.5417, Pan -/+2500, ding on A2. Fix the timeline offset (see lessons above) and ask the user for missed items, sold items, purchases while dead.
3. **Captions on loud moments:** earshot `find` + transcript; below the action (lower centre, y about 1215 of 1440, size about 100 to 120, slam-in); few and strong; user prunes them.
4. **User edits in Resolve** (cuts, dissolves, moved cards). Always work on a duplicate (v3), re-read V1 and re-map times before each change.
5. **Export:** Resolve H264_NVIDIA to `work/render_big/` (about 5 min, 30 GB), ffmpeg NVENC re-encode into `export/` (about 9 GB at cq 21; use a higher cq for smaller), delete the big file.
6. **Thumbnails (long):** `core/scripts/thumb_mix.py` (5 variants, fat theme-colour border). **Covers for shorts:** `core/scripts/thumb_short.py` (3 per short, 1080x1920).
7. **Shorts:** `core/scripts/gaming_shorts.py` from the final export; the user supplies timestamps for clips they know are good; no held end frame, "FULL VIDEO / LINK BELOW" over the last 2 s of live footage (`end_over`).
8. **Titles, descriptions, tags:** `workflows/packaging-youtube-tiktok.md` (shorts: small version). Tags for Graves shorts: Deadlock, Deadlock gameplay, Deadlock Graves, Deadlock Graves gameplay, Graves, Graves gameplay, Graves Deadlock, Deadlock highlights, Deadlock gameplay highlights, Deadlock clips, Deadlock shorts, Deadlock best moments, Deadlock hero shooter, Valve Deadlock, Deadlock PC gameplay, <channel_name>.

What the user liked: item cards (left edge, wiki tooltip, ding), fewer stronger captions, thumbnails ("great"), shorts that conclude the game or show a clear moment (83,000 damage ending, triple kill).
What the user did not like: automatic trimming of inactive time (earlier), weak/random action shorts, dead seconds at the end of shorts.

## Next-video improvements the user asked for (2026-11-12)
- **Better thumbnails:** do not reuse the general wiki `Render.png` for every thumbnail. Build the character pictures from the hero's **reference sheet** (`Reference_Sheet.png`, plus the 3D model `<Hero>.glb` in `<GRAPHICS_DIR>\Deadlock heroes\<Hero>`) so the hero looks exactly as in the gameplay, in different poses and angles that fit each thumbnail idea. Not started. Open point to settle with the user first: our hard rule is "no AI-generated pictures", so the route is cut-outs from the sheet and posing the real 3D model (Blender is not installed: ask before installing); generating new art with an image model needs the user's explicit OK.
- **Video editing a bit better** (the user wants improvement; specifics not given yet): ask what first. Known weak spots from this video: purchase detection misses items (Grit, Weapon Shielding, Stamina Mastery), no own-hero/alive detection yet, captions timed from Whisper words needed manual moves, first cards were 2 s late (fixed rule above).
- To build: alive/own-hero detector (portrait highlight and red respawn countdown), "ITEM SOLD" vs "PURCHASED" reading, caption snap to the voice onset.
- Raw recordings: after the video is exported the user may delete the raw from C: Videos (Graves gameplay 2 did); do it only after asking (permanent, timelines go offline); an alternative is moving it to the project's raw/ on D: and relinking.
