# Style guide: vlog and short-form talking videos (an example creator's channels)

What the user likes, learned from feedback while editing. Applies first to the vlog channel (vertical shorts); reuse it for any short-form talking video and adjust only when the user says so. Update this file whenever the user gives new feedback.

## Format
- Vertical 1080x1920, 30 fps, h264. Phone footage is vertical (see `davinci-playbook.md`, rotation).
- Length: shorts are tight. Every cut is quick; no dead air, no false starts (silence removal at -32 dB, 0.45 s minimum, 0.10 s padding is the setting the user judged "not too tight").
- No AI-generated footage or pictures. Real footage, the user's own images, simple graphics and text only. Keep it engaging without overdoing it.
- Make the video complete first (story and cuts), then add graphics, transitions, sound, colour, audio cleanup. Always in that order.

## Pacing and zoom
- **Dramatic opening:** the first clip starts with a very fast punch-in (about 7 frames, under a quarter second, to roughly 1.25x) with motion blur and a big whoosh, then slowly zooms back out until the clip ends. A slow creeping zoom for the whole first clip felt flat.
- **Alternate zoom and no zoom:** every 2 to 3 clips one clip does a slow push-in from 1.00 to about 1.10 to 1.15 over the whole clip; the next clip is unzoomed, so the cut itself feels like movement. Zoom also hides jump cuts between takes.
- Do not zoom clips shorter than about 1.3 s (it looks jittery). Zooms stay small (1.07 to 1.16); only the opening punch goes higher.
- Time-lapses: a long action becomes 2 to 4 s (roughly 120x to 190x), pre-rendered, with a slow push-in or pull-out. Quick real-time shots of the key moments (0.5 to 1.5 s each) alternate with them.
- Match B-roll to the voiceover phrase by phrase: each phrase of 1 to 3 s gets one to three clips that show what is being said (tool, action, result). The user gives timestamps of the action in the raw clips; use a second or two around them. B-roll audio is never used (muted).

## Transitions
- Every transition is quick: 6 to 10 frames (0.2 to 0.35 s), never more than half a second. A long dissolve was rejected.
- Between sections use a **slide/push with real motion blur and a whoosh** (outgoing clip pushes right, incoming slides in from the left); anything sitting on top (picture cards) rides out with the same motion.
- Quick cross dissolves are fine at B-roll boundaries and time-lapse edges. Inside a section use hard cuts. Never a dip to black.

## On-screen text
- Heavy sans (Arial Black) with a thick dark outline and soft shadow, slightly tilted (2 to 3 degrees), placed low-middle or top, never over the mouth.
- **Text colour: clean white with one solid accent colour.** History: all-white text felt flat; gradients plus glows in many hues felt "weird"; the user then said plain white was worse than that and gave a reference (a YouTube Shorts caption: bold clean sans, most words white, the key word in one solid green, no gradient, no glow). Chosen style: heavy clean sans (Segoe UI Black), words in white and the key word of each text in **one flat accent colour** (two accents in total: yellow `[255,205,40]` for the old, the problem or the attention word such as "13", "YEARS", "DRYER"; green `[40,205,75]` for the new or the working word such as "Duo", "ID", "WORKING"), a thin near-black outline and a soft shadow for legibility on footage. No gradients, no glow unless the user asks. A plain white version and a gradient/glow version were also exported for comparison, so the user can choose; offer variants rather than guessing when taste is unclear. The user can give reference images for style; study them and match.
- Key phrases (for example "4 YEARS") slam in: start 2.6x size with blur, land hard, shake a little, brief white flash; exit with a small pop-out. Keep them modest in size: a big number (about 400 px font) over a smaller word (about 150 px); the first, bigger version was "too big". No subtext unless asked.
- Object labels ("HAIR DRYER", "FACE ID") are smaller (about 125 to 135 px), pop in as the word is spoken, hold about 0.7 s, pop out. A second line may hold the user's own words ("KEEPS WORKING").
- **Text behind the speaker** (depth text) for names or key terms in the opening: the text sits behind head and shoulders, partly hidden by the hat, while a phone held in front of the body stays in front. It fits inside 1080 px and replaces itself ("iPhone 13", then "iPhone Duo") as the words are spoken.
- Captions (when asked): Roman Urdu for Urdu words, English words as written. English-only videos get English labels. Captions only when the user asks, whole video or part.

## Pictures and inserts
- Never full screen. Small rounded white cards (about 260 to 330 px wide), slight tilt, a soft shadow; motion blur on every move.
- Several pictures that belong together appear **all at once** (pop in together with one pop sound), not one by one, and leave together.
- Card entrances: slide in from the side with a small settle, or pop (scale up with a slight overshoot). Hold about a second; exit by sliding out.
- Keep them off the face: bottom strip or top corners (on close-ups the chin sits near two thirds of the frame height).
- Pair cards can enter first and second (camera card top-left, then the game banner top-right) and leave together; each needs its own video track.
- Use the user's own pictures from the project's reference folder. If a picture is needed and none is supplied, ask before downloading; free-licence images (Wikimedia Commons) need a credit line in the description. A crop of the user's own footage is an acceptable stand-in.
- Transparent pictures (PNG, WebP, AVIF) go on a white card so dark subjects stay visible.

## Sound
- Pop for pop-ins; whoosh for slides, slams and card entrances. If a whoosh already covers an element, drop the pop.
- Place each sound so its loudest moment lands on the action (see peak table in `effects-toolkit.md`). Use a bigger, more dramatic whoosh for the opening.
- Effects sit 3 to 6 dB under the voice. Voiceover tracks get voice isolation plus dialogue leveler, then per-clip gain to even out the speech level (target about -18 dBFS).

## Colour
- **Vivid, not flat** (user wants it more colourful than the first subtle pass). Neutralise the cast on B-roll (a white table should read white), keep skin natural but richer: slope 1.04, power 0.95, CDL saturation about 1.34 on faces and 1.42 on B-roll gave roughly +17% (faces) and +32% (B-roll) measured saturation with no extra clipped pixels. CDL saturation is relative to what is already on the clip; measure the result on a render rather than trusting the number. Done as a single CDL per clip so it is easy to zero; check the skin does not turn orange.

## Voiceover and structure
- Voiceover is recorded in Resolve after the picture is rough cut, one phrase per take; the user chooses takes. Bad takes are never auto-picked.
- Opening states the reason and stakes in one or two sentences, then goes straight to the action. Ending is short, sets up the next video and asks for a follow.
- **Follow call-to-action text:** during the last spoken "please follow" line (about the last second) show "FOLLOW" over "SUBSCRIBE" in the accent text style (white "FOLLOW", green "SUBSCRIBE"), **centred**, two stacked lines with a tight gap (stack gap -0.34, no slash, no tilt), low in the frame (y about 1560), popping in and staying to the last frame; no arrow, no sound effect. A first version with "FOLLOW /" wrapped awkwardly and looked off-centre; measure the text box centre (should equal 540) before delivering. Template: `core/templates/overlay_specs/cta_follow_subscribe_accent.json`. Give the overlay two spare frames so it covers the very last frame.
- The user may speak a clip several times; the last complete take is a suggestion only, since only the user knows which looks best on camera.

## Tech shorts (wireless mic mic test, 2026-11-10; first pass, awaiting the user's feedback)
- Comparison shorts: four stacked 1080x400 strips, active one in colour with a speaker icon, the others grey and frozen, caption band below. Labels top-left of each strip, accent colour on OFF/ON.
- Fast pacing: silence cut (-32 dB), small push-in on every second cut, a whoosh on the opening punch, pops on labels. Always end with "FULL VIDEO / LINK BELOW" over a held last frame when speech ends.

## Captions, fun version (user feedback 2026-11-10: "more fun, colours, animations coming up, fast paced, no gradients or glow")
- Word-by-word: each word pops in as it is spoken (scale 135 to 100 in about 0.1 s), the current word in a flat bright colour (cyan, pink, orange, lime, yellow rotating per line), earlier words white; brand and ON/OFF words keep fixed colours (OFF and Fifine yellow, ON and Hollyland green). Lines are 2 to 3 words, slightly tilted (alternating +-2 degrees), slide up 40 px on entry. Heavy sans with dark outline, no gradient, no glow. Tool: `core/scripts/caption_overlay.py`. In stacked-panel layouts the caption sits at the bottom of the active panel.
- Resolve shorts: take the user's own timeline and audio as the base; do not rebuild from raw takes (the user rejected that).
- Premium look (user, 2026-11-10): plain white text with a soft black shadow, no colours; sized modest, static (no pop) on still shots, tightly stacked lines, level. In colour-managed projects overlay white needs a CDL slope of about 1.8 on the overlay item to render as true white.

## Voiceover from raw takes and B-roll (user, 2026-11-16)
- No gaps between voice takes: trim each piece to the speech edges and join with no pause. Cut false starts inside a line ('fix' before 'fixing').
- When a line mentions a thing, show that thing at that word (capture card box, then the connection; PlayStation when 'PlayStation' is said). Split one voice line into two clips at the word where the subject changes.
- Use real-time slices (clips shot at 120 fps) for short key actions, time-lapse for long ones; do not time-lapse everything.
- Time-lapse clips with long no-action parts or other people walking in: use only the action part.
- Lines the user will supply footage for are left empty on V1 with a red marker; keep a list. Rejected takes are marked red with the reason in the takes timeline.
- When the user shows a timecode range of wrong audio, convert it to source time and snap the cut to the silence dips; if the range ends inside a word, keep the word and say so.
- Do not make everything a time-lapse (user, 2026-11-16): keep time-lapse for moving/long actions, show a good scene at normal pace for cables, boxes and connections. Always check which raw clip the user means by the object (the UGREEN box was in 1694, not 1700).
