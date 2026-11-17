# Verification and token saving

Why: pictures that Claude looks at (screenshots, Resolve thumbnails, `get_image`) cost image tokens. Local checks on a render return a few numbers and cost almost nothing. The user asked for this.

## Method
1. Render the timeline through Resolve to an mp4 (about 5 to 15 s, see `davinci-playbook.md`).
2. Run OpenCV, numpy and ffmpeg on the file. Return numbers only.
3. Look at pictures only for taste or layout questions, and then as one small contact sheet (several frames about 300 px wide in one image, built locally) instead of many calls.

## Checks that found real problems or confirmed changes
- **Dark or black frames:** mean brightness per frame (OpenCV `frame.mean()`), flag below about 12. A transition that dipped to black for 8 frames was invisible in thumbnails and obvious in the numbers. Check the brightness around every transition (min versus the neighbouring frames).
- **Frozen frames:** mean absolute difference between neighbouring frames below about 0.02.
- **Gaps and length:** compare item end frames to the next item's start frame from the timeline data; compare total frames of the render to the timeline length.
- **Did the change land:** mean absolute difference between two renders at a chosen frame (overlay present, text visible, grade applied); then compare measured values (for example the RGB of the table before and after).
- **Colour balance:** average RGB of the brightest 25% of a frame, before and after.
- **Audio:** decode to 16 kHz mono and compute RMS in 100 ms windows. Report speech level per clip (windows above -45 dBFS), spread and standard deviation across clips, noise floor (10th percentile), peak. Peak at full scale means clipping or an effect on top. Compare the render before and after a change.
- **Timing of words:** transcribe the render with word timestamps to confirm that text, pictures and sounds land on the intended words.

## Pitfalls seen
- Thumbnails sample a few frames and miss one-frame problems.
- Sound effects overlapping a voice clip raise its measured level; interpret per-clip results with that in mind.
- Resolve renders the current timeline, so set it current first (this changes what the user sees in the app); render the older version too when you want a before/after comparison.
