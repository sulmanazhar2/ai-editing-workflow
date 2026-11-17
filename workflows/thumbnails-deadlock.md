# Workflow: Deadlock thumbnails (style notes from the user's own work)

Status: style learned from reading folders #24 to #39 in `<GAMING_ARCHIVE>` (2026-11-11). Hero art library downloaded 2026-11-11 and reference thumbnails from other channels added (sections at the end). Builder script `core/scripts/make_thumbnail.py` built 2026-11-11. Update this file with every correction from the user.

## What a folder contains
Each video folder `<GAMING_ARCHIVE>\<N>. <title>\` holds the user's thumbnail inputs and the result:
- **Final = `BIG BALLS (n).png`** (1280x720). "BIG BALLS" is the user's template/project name; the number counts copies, so the highest number in a folder is the latest. Some folders have no such file: then the final is `Thumbnail.png` or an AI-art image with text already on it (Bebop, Drifter), or the folder only has a vertical short cover (#33).
- **Wiki pictures** (from deadlock.wiki, already in the folders): `<Hero>_Render.png` (full body cut-out, the main subject), `<Hero>_Reference_Sheet.png`, `<Hero>_Gloat.png` / `_Critical.png` / `_card.png` (face/pose crops), `<Hero>_name.svg.png` or `_vertical.png` (the hero's name wordmark), ability icons (`Spirit_Burn.png`...).
- **Extra art the user made elsewhere** (ChatGPT/Gemini images, often run through Photoroom for a transparent cut-out: `...-Photoroom.png`): stylised hero pictures with a theme background, items (umbrella for Mina), lineups of several heroes (#35). The user chose these themselves; the workflow only uses pictures the user supplies or the wiki ones and does not generate any.
- Screenshots of the game (#35) for picking the heroes/pose.

## The look (stable across #24 to #39)
- **Size 1280x720**, hero on one side, big text on the other.
- **Hero**: a large cut-out (wiki render or the user's art), 50 to 60 % of the frame, allowed to run off the frame edges (cropped at the bottom, a hand or gun reaching out). Face is clearly visible and expressive (grin, thumbs-up, guns crossed). Facing/pointing toward the text or the viewer.
- **Background**: flat or lightly textured dark colour taken from the hero's palette (maroon, dark red, brown, dark orange, dark purple); often a darkened, blurred copy of the hero art or other heroes ghosted behind. No busy gameplay screenshots.
- **Text**: 2 to 5 words, huge, bold condensed/rounded display font in 2 to 4 stacked lines, thick dark outline and/or shadow. Mostly white or one warm colour, with **one or two words in an accent colour** that carries the point (GROUP green, HUNTED red, BABA purple, DEADLY lime, RAINBOW/TOO TANKY purple-blue gradient). Text colour is chosen to contrast with the background and match the hero (Celeste purple, Dynamo orange-yellow, Vindicta orange, Infernus black with white outline and yellow words).
- **Wording**: a claim or hook about the hero in the video, not the video title: "TOO TANKY / RAINBOW STATS", "BEST KIT IN THE GAME", "JUST CAN'T DIE", "THEY WEREN'T READY", "SQUISHY BUT DEADLY", "WAY STRONGER THAN BABA", "SHOTS TOO HEAVY", "WALK IN A GROUP OR GET HUNTED", "NO ONE DIES ON NURSE WATCH", "GOAT OF NEW YORK", "TAKE THIS BOMB".
- **Branding**: small Deadlock logo with its wordmark in a bottom corner (bottom right most often, top left in a few). Hero name wordmark small in a top corner when it helps (Nurse Harrow, Rat King, Dynamo, Deadman Danny).
- **Border**: the earlier ones (#26, #29, #37, #39) have a thin coloured rounded border (purple, yellow, pink, tan); later ones are full bleed. Ask which one is wanted now.
- **Extras when relevant**: a row of 3 ability icons under the text (Mina, "squishy but deadly"); other heroes shown with a coloured glow outline (green glow for the heroes a healer supports, #35); a stat badge such as "Most Damage 57.2k" (Drifter).
- **Newest direction (#37 Drifter, #38 Bebop)**: fully illustrated, high-contrast single-colour theme (all red for Drifter, fiery orange for Bebop), hero close and dramatic, slanted italic brush text (white with the keyword in red), more polish than the older flat-colour ones. The user made several versions per video; the last one is usually the final.
- **Vertical covers** (#33 shorts): 1080x1920, same ideas: hero at top, huge text in the middle, logo at the bottom.

## Proposed process for the workflow (to confirm with the user)
1. Input: the hero, the video's one-line angle (or I suggest 3 hooks from the video: loudest or most surprising moment, build, result), and the pictures in the video's folder.
2. Take the wiki render/cut-out from the folder (already downloaded for all heroes in `<GRAPHICS_DIR>\Deadlock heroes\<Hero>\`, see the library section).
3. Pick the background colour from the hero image's dominant colours, make a darkened blurred copy of the hero art behind, place the cut-out large and off-edge, add text with outline and accent word, logo, optional name wordmark / ability icons.
4. Build locally with Pillow (fonts, outline, gradient, shadow) at 1280x720 (and 1080x1920 for shorts); output 3 variants (different hook or layout) to the video's `export/` or the user's folder; the user picks, corrections go into this file.
5. Fonts: the user's exact fonts are unknown (looks like Bebas/Anton/Lilita One style heavy display fonts); ask for the names or find the closest free ones.

## Open questions for the user
- Which tool do you build thumbnails in now (Canva, Photopea, Photoshop), and do you have the template file (the "BIG BALLS" one) and the font names?
- Border or full bleed, and which of the older or newer looks to follow for new videos?
- Should the workflow make the hero cut-outs itself (background removal with a local model) when only a wiki render or an AI picture is given?
- Where should drafts go, the project `export/` or the video's folder in `<GAMING_ARCHIVE>`?

## Hero art library (downloaded 2026-11-11)
- `<GRAPHICS_DIR>\Deadlock heroes\<Hero>\` for all 44 heroes (363 MB), made by `core/scripts/fetch_hero_art.py` from deadlock.wiki's MediaWiki API (`api.php`, files on `cdn.deadlock.wiki`). `index.json` in that folder lists each hero's files.
- Files per hero (when the wiki has them): `Render.png` (full body, transparent, about 1500x1400: the main thumbnail subject), `Reference_Sheet.png` (turnaround sheet up to 5000x3000: other poses and angles, cut from it if the Render pose does not fit), `name.png` (wordmark), `Gloat.png` / `Critical_Health.png` / `card.png` (face crops, 280x380, too small for a big face), `select_background.png` (hero on a coloured stage, about 2800x2300), `TitlePage.png`, `Weapon.png`, `vertical.png`, `icon.png` (128 px), and `<Hero>.glb` (the 3D model, about 1 MB; could be posed and rendered in Blender for a new angle; Blender is not installed, ask first).
- New hero or missing file: `python core\scripts\fetch_hero_art.py --heroes "Graves" --glb` (skips what exists). Wiki file names use underscores (`Lady_Geist_Render.png`).
- Item and ability pictures are in `<GRAPHICS_DIR>\Deadlock abilities`.
- If the user only says "make a thumbnail for Graves": default is Render + name wordmark + Deadlock logo, then offer the other pictures.

## Reference thumbnails from other Deadlock channels (user supplied 2026-11-11; learn the ideas, never copy)
Three channel pages, as screenshots:
1. **Illustrated hero in one colour world (build/gameplay channel).** All green for a health build ("YOUR HEALTH IS MINE", glowing aura, near-black silhouette), all red for "NERFED" (hero on a red cape, tall condensed red text with heavy outline), neon magenta for a spirit hero, blue/cyan with a huge italic wordmark "GRAPPLIN". Dark blurred city street behind. Hero large, rim-lit, action pose, with effects that say the point (energy streaks, explosion for a spirit nuke, green healing cross). Event videos (Street Brawl): a lineup of several heroes with the title across the top in yellow or cyan. Text is 1 to 3 words at the very top edge, partly overlapped by the hero's head. Thin rounded border on some.
2. **Cat-meme channel.** Rounded white border on every thumbnail. Large hero/item art plus a recurring mascot (a caracal meme cat) in the bottom-right corner: the series signature. Giant cut-off headline words behind the hero ("BROKEN", "NURSE HARROW", "DEADMAN DANNY", a script signature "Violet") with the character overlapping the letters. Small game UI pieces (ability icons, scoreboard rows) as proof for the claim.
3. **Series banner.** The same giant serif channel wordmark across the top of every thumbnail, cropped by the frame, with the hero (in-game render, weapon pointing at the viewer) below it and overlapping. Titles are lowercase conversational sentences; no other text on the thumbnail.

### Takeaways (our own twist)
- One hero, one colour theme taken from the hero (green, red, purple...). Rim light plus glow in the theme colour, dark blurred city behind.
- Hero overlaps the text (text behind the hero): depth. Cut-off oversize words are fine when the hero hides part of them.
- Hook of 1 to 3 words, huge, at the top, keyword in the accent colour. It must read at about 168 px wide on a phone.
- Effects that carry the video's point (aura, streak, explosion, healing cross, item icons as proof, "NERFED" / "BROKEN" claims).
- One brand constant on every thumbnail (border, corner logo, mascot or series banner); ask the user which.
- Group videos: lineup of 3 to 5 heroes, title across the top.
- Check at about 320x180 on dark and light pages before showing.

## Build recipe with the library (Pillow, local; to implement as `core/scripts/make_thumbnail.py`)
1. Pick the hero and the hook. Load `Render.png`, trim transparent margins, scale to about 85 to 100 % of the frame height, place off-centre, let a hand or weapon run off the edge.
2. Background: dominant colour of the Render (or the user's theme colour), gradient + vignette, darkened blurred `select_background.png` or the Render, optional city blur from a game screenshot the user supplies. No AI-generated images.
3. Glow: blurred copy of the Render's alpha tinted in the theme colour behind the hero; a thin rim light in the accent colour.
4. Text: heavy condensed display font (Anton, Bebas Neue, League Gothic, Lilita One as candidates; need the user's choice), 2 to 3 lines, stroke + shadow, accent colour on the keyword; text behind the hero by masking with the Render's alpha.
5. Branding: Deadlock logo in a bottom corner, hero `name.png` small when useful, ability icons from `Deadlock abilities`, optional border or mascot.
6. Output 3 variants at 1280x720 (and 1080x1920 for shorts) to the project's `export/` for review; record corrections here.

## Decisions and tool notes (2026-11-11)
- User has no brand constant and no font preference: mix it up, I choose. Font used: Impact (Windows, `C:\Windows\Fonts\impact.ttf`); alternatives `--font black|bahn`. Deadlock logo file not found on disk; the hero `name.png` wordmark is used as the corner mark. Ask the user for a logo file if wanted.
- `make_thumbnail.py` variants: A hero right, text left; B text on top, hero centred below (oval-faded edges); C hero left, text right. The wiki `Render.png` is key art with its own circular backdrop (not a clean cut-out), so true text-behind-hero is not possible with it; the script feathers it into the scene. For a clean cut-out, use the user's Photoroom images (`--art path` expects a file inside the hero folder, copy it there) or cut with a local model later.
- Not done yet: the user's colour/effects ideas (aura, streaks, item proof icons beyond `--extra`), group lineups, 1080x1920 cover check, small-size test. First test: Graves "SHE NEVER DIES" in `projects/gaming/3. Graves gameplay 2/work/thumb_test/`; user has not reviewed it yet. Draft previews go to the project's `work/` until the user picks, finals to `export/`.

## Graves gameplay 2 thumbnails (2026-11-12): `core/scripts/thumb_mix.py`
- User brief: title "my best Graves games ever"; thumbnail words about fire, zombies, highest damage, MVP; **fat border in the colour of the image's colour profile**; 5 mixed versions from Graves art plus other sources.
- Script makes 5 variants at 1280x720 with a 30 px rounded border in the variant's theme colour (green, orange, cyan, red): A "HIGHEST DAMAGE" + MVP badge, B "ON FIRE" (gameplay frame backdrop + procedural flames), C "ZOMBIE ARMY" (zombie cut-outs keyed from `Reference_Sheet.png`, skull pattern from the sheet's skull silhouettes), D "83K DAMAGE", E "ZERO DEATHS". Facts used only from the user: most damage, 83k damage, no deaths.
- Method notes: gameplay frames (centre crop, blurred, dimmed) work as backdrops; flames are procedural noise (rows run top to bottom, remember row 0 is the top); the hero Render needs a soft edge on the side facing the text.
- Drafts were in `projects/gaming/3. Graves gameplay 2/export/thumb_*.png`; record the user's pick and corrections here.

## AI character art, local (set up 2026-11-15)
- Why: the wiki art cannot show new poses or moods ("menacing Graves"). User used to do this by hand in ChatGPT with the hero reference sheet; now local and free. Allowed by the user for thumbnails only (CLAUDE.md rule).
- Stack: ComfyUI in `tools/comfyui` (own venv), Qwen-Image-Edit 2511 Q4_K_M GGUF + Lightning 4-step LoRA + qwen 2.5 VL fp8 text encoder (about 23 GB on D:). About 30 s per image on the 5070 Ti (first one 56 s). Background removal: `rembg` (isnet-general-use) in the main `.venv`, then fill holes in the mask (the plaid skirt came out see-through without it).
- Tool: `core/scripts/gen_character.py --out <dir> --ref sheet_front.png --ref render.png --prompt "..." --n 5 --seed 11` (starts ComfyUI itself; `--stop` frees the GPU). Max 3 reference images, they appear as "Picture 1/2/3" in the prompt.
- References that worked: Picture 1 = a tight crop of the hero's front view from `Reference_Sheet.png` (upscaled 2x; the full sheet is too small once squashed to 1 MP), Picture 2 = `Render.png` on grey (gives the painted game style). Prompt pattern: name the costume details from Picture 1, "pale skin ... exactly like Picture 1", "painterly stylized 3D game-art style of Picture 2, not flat cartoon", pose + expression, "plain flat dark grey background, no text".
- Known weaknesses: colours drift (Graves' jacket maroon became bright red), first try without "pale skin" gave a tan face and cartoon look, extreme "menace" gives shark teeth, so ask for "smirk/grin". Multi-hero images not tested yet.
- Thumbnails: `make_thumbnail.py --art <abs path to cutout png> --bust` (bust = waist-up art, keeps the head in frame). Test run: `test/thumbnails/graves/` (refs, gen1, gen2, cut, final/graves_thumb_1..5.png). Not reviewed by the user yet; record the feedback here.
- **Round 2 (2026-11-15), user feedback on round 1:** liked graves_thumb_3 (pointing, laughing) best; colours too saturated ("looks like AI"); wants more parts of the reference sheet used (the dead hands, zombies). Fix that worked: Picture 1 = **upper-body crop** (`sheet_upper.png`), Picture 3 = a sheet part (`sheet_hand1.png` dead hand, `sheet_zombie.png` skull ghoul, cropped from `Reference_Sheet.png`), prompt says "COLOURS MATCH Picture 1 EXACTLY: muted desaturated, dark dull maroon jacket (not bright red), nothing neon", and put "IGNORE the standing pose and neutral face of Picture 1, use ONLY the pose/expression described" first. Without that the sheet's pose and blank face leak in (full-body, neutral). Results: `test/thumbnails/graves/gen4`, `cut4`, `final2/graves_v2_1..5.png`. `core/scripts/cutout.py` = rembg + solid fill. Remaining: grey background patches left between arm and body in some cutouts, name wordmark can overlap the character, backgrounds still plain green blur.

## Thumbnails with the creator's own face
- Real photo cutouts beat AI faces (exact likeness, no drift). Keep the cutout clean (no chair or microphone left in the picture), put the face on the side it looks toward, and keep text and logo on the opposite side. Use official art unaltered for game logos and do not place text over them.
