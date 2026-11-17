# DaVinci Resolve Studio playbook (through the MCP)

Resolve Studio 21.2 is driven from Claude through the `mcp__DaVinci_Resolve_Studio__*` tools (an older `mcp__DaVinci_Resolve__*` set also exists; use the Studio one). The tools are deferred: load them with ToolSearch first (`get_resolve_status`, `launch_resolve`, `run_script`, `search_scripting_api` (argument `pattern`), `get_scripting_api`, `get_image`, `get_thumbnails`). `run_script` runs sandboxed Python with `resolve` and `project` injected; it has no filesystem access (Resolve itself reads the paths you pass). Returning a non-serialisable object (a Fusion input, for example) makes the whole call fail after the work was done, so return plain values and check state with a second call.

## Safety rules
- Add new timelines; never delete or alter the user's own projects or timelines. Work on `DuplicateTimeline("<name> vN (what changed)")`.
- The user edits at the same time: re-read the timeline (items, tracks, frames) before every change; their trims and moves are the truth. Frame numbers shift by a frame after duplication, so look items up by their current start frame.
- Deleting old timelines or media pool items only when the user says so.

## Timecodes, frames, rates
- The user's times are timecodes (SS:FF). Timeline frames are at the timeline rate (30). Source frames are counted in the clip's own rate (59.94 for the 60 fps phone clips). Audio source frames count 60 per second.
- Phone footage is vertical via a rotation flag: ffprobe shows 1920x1080, Resolve and ffmpeg autorotate to 1080x1920. A vertical short needs a 1080x1920 timeline (`Timeline.SetSetting("useCustomSettings","1")`, `timelineResolutionWidth`, `timelineResolutionHeight`); in a 1920x1080 timeline the footage gets black bars.

## Basic operations
- Projects and media: `GetProjectManager().GetCurrentProject()`, `GetProjectListInCurrentFolder()`, `MediaPool.ImportMedia([paths])`, `MediaPool.RelinkClips(clips, folder)` after raw files move, `MediaPool.ImportTimelineFromFile(path, {"timelineName":..., "importSourceClips": True})` (FCPXML from `tl.py` imports correctly).
- Read a timeline: `GetItemListInTrack("video"|"audio", n)`, item `GetStart/GetEnd/GetName/GetType/GetSourceStartFrame/GetSourceEndFrame/GetMediaPoolItem().GetClipProperty("File Path")`.
- Place clips: `MediaPool.AppendToTimeline([{mediaPoolItem, startFrame, endFrame, mediaType (1 video only, 2 audio only), trackIndex, recordFrame}])`. Quirks: a video clip comes out one timeline frame shorter than asked for (N source frames at 59.94 fps give N/2 - 1 frames); a clip placed over another on the same track overwrites it, so overlapping graphics need their own track (`Timeline.AddTrack("video")`); still images ignore `endFrame` and are 5 s long (convert stills to a clip of the right length first); to make a clip end exactly on a frame use `recordFrame = end - (N // 2 - 1)`; for gap-free real-time clips ask for 1 to 2 extra source frames so the next cut overwrites the overhang.
- Delete: `Timeline.DeleteClips(items, False)` (False = no ripple). Transition items are deleted the same way.
- Audio: `SetProperty("AudioVolume", dB)`, `AudioVoiceIsolationEnabled` plus `AudioVoiceIsolationAmount` (0 to 100), `AudioDialogueLevelerEnabled` plus `AudioDialogueLevelerReduceLoudDialogue` / `AudioDialogueLevelerLiftSoftDialogue` (active timeline only). Sound clips overlapping on one audio track overwrite each other: use different tracks or trim with `endFrame`.

## Animation (Fusion keyframes, verified)
```python
comp = item.AddFusionComp()          # or item.GetFusionCompByIndex(1)
mi, mo = comp.FindTool("MediaIn1"), comp.FindTool("MediaOut1")
t = comp.AddTool("Transform", 0, 0); t.Input = mi.Output; mo.Input = t.Output
end = comp.GetAttrs()["COMPN_RenderEnd"]      # comp time is in the clip's own frames
t.Size = comp.BezierSpline({}); t.Size[0] = 1.0; t.Size[end] = 1.1   # smooth push-in
t.Center = comp.XYPath({}); t.Center[frame] = [x, 0.5]              # position animation
t.MotionBlur = 1; t.Quality = 16; t.ShutterAngle = 150              # real motion blur
```
- Several keys give the curve shape (opening punch: 1.00 at 0, 1.17 at 4, 1.26 at 7, back to 1.00 at the end).
- The built-in Dynamic Zoom only offers ease modes and its direction is fixed; use Fusion for push-in/pull-out.
- Fusion tool inputs read back as dicts (keys 1, 2) and tool objects are not JSON-serialisable.

## Transitions
- `item.AddTransition({"type": "Cross Dissolve" | "Slide", "category": "simple", "position": "start"|"end", "alignment": "center"|"left"|"right", "duration": frames})`. Both clips need spare frames (handles) on the side the transition uses. Pre-rendered clips (time-lapses) have none: attach so only the clip with handles supplies them (`"left"` when the incoming clip is pre-rendered and the cut is at the incoming clip's start; adding to the outgoing clip with `position "end"`, `alignment "left"` avoided a dip). **A transition can silently dip to black**: check brightness on a render (see `verification.md`).
- A transition that comes out 1 frame long can be repaired with `transition_item.SetProperty("TransitionDuration", 8)`.
- Resolve's own transitions have no motion blur setting. For a slide/push with real motion blur, animate Fusion `Center` on the outgoing clip (in-out cubic) and put the first frames of the incoming clip (taken from before its in-point, so timing stays continuous) on the track above sliding in on the same curve; set motion blur on both; render cards on top to leave with the same motion (`overlay_anim.py`, `"out": "slide-right"`, `"slide_dist": 1150`, `subframes: 24`). Place a whoosh with its peak mid-slide.

## Colour
- `item.SetCDL({"NodeIndex": "1", "Slope": "r g b", "Offset": "0 0 0", "Power": "r g b", "Saturation": "s"})`. Measure first (see `effects-toolkit.md`, colour) and keep changes small.

## Render
- `proj.SetCurrentRenderFormatAndCodec("mp4","H264_NVIDIA")`, `proj.SetRenderSettings({"SelectAllFrames": True, "TargetDir": ..., "CustomName": ..., "ExportVideo": True, "ExportAudio": True})`, `AddRenderJob()`, `StartRendering(job)`, poll `IsRenderingInProgress()` and `GetRenderJobStatus(job)`. A 30 s vertical draft renders in 5 to 15 s. The target folder must exist. Use this render for transcription and for all numeric checks.

## Final export for Instagram or other platforms
- Vertical short: mp4, H.264, 1080x1920, 30 fps, AAC 48 kHz stereo 320 kbps (Resolve's NVIDIA H.264 default gave about 16 Mbps, 61 MB for 31 s; fine). `SetRenderSettings` with several keys in one call can return False and still apply some keys (TargetDir and CustomName applied); set keys one at a time and read the returned flags. `VideoQuality` and `AudioBitRate` were rejected; the defaults were fine. Create the target folder first.
- **Check the audio of the export:** the first export measured -11.9 LUFS with the peak at 0 dBFS and 582 clipped samples (voice gain, leveler and effects stacked). Fixed with `ffmpeg -i in.mp4 -c:v copy -af "volume=-2.2dB,alimiter=limit=0.89:attack=3:release=60:level=false" -c:a aac -b:a 320k out.mp4`, giving -14.1 LUFS and a -1.9 dBFS peak. Leave the voice at a normal level (around -14 LUFS in total) so the platform's music can sit under it. Always measure (`ffmpeg ... -af ebur128=peak=true -f null -`) before handing a file over.
- Background music: the user adds it inside the platform's own library (licensed and cleared there), so the export is clean voice plus effects. The assistant cannot choose music by taste or reach Instagram's library.

## Seeing frames
- `get_thumbnails` (grid of timeline frames) and `get_image` cost image tokens. Use them sparingly and as one contact sheet; prefer numeric checks.

## Not available through the API
- No direct keyframes on inspector properties, no Magic Mask control, no transition motion blur, no listing of transition names (names that worked: "Cross Dissolve", "Slide"; other names are untested).

## Editing text inside compound clips (no API for it)
The API cannot open a compound clip or change Fusion text inside it. Recipe that worked: `Timeline.Export(path, resolve.EXPORT_DRT, resolve.EXPORT_NONE)` (a DRT is a zip of XML; FCPXML drops Fusion titles). Titles live in `SeqContainer/*.xml` as `<CompositionBA>` (hex: 4-byte big-endian length + zlib); inside, the Fusion comp text ends with `Compressed = true, }` followed by 1 byte, a 4-byte little-endian length and a second zlib stream holding the tools (`StyledText = Input { Value = "..." }`). Patch the text, keeping the **same length** (Resolve silently dropped a comp whose size changed), recompress both layers, rezip. Importing it into the same project reuses the existing compound by ID, so give the compound clips and containers fresh GUIDs (plain, no-dash and UTF-16-hex forms; see `projects/tech/1. wireless mic/work/patch_drt.py` and `remap_drt.py`), then `MediaPool.ImportTimelineFromFile(path, {"timelineName": ...})`. Failed attempts leave extra timelines and pool clips: rename them and ask before deleting.
- The ffmpeg `ass`/`subtitles` filters drop transparency: render captions in colour on black plus a white-on-black mask and `alphamerge` (done in `core/scripts/caption_overlay.py`).
- Verified again: `AddFusionComp()` on stills and on compound items works, with Transform Size keys; `run_script` fails if the returned dict holds Resolve objects. Resolve's H264 render of a vertical short came out at about -22 LUFS: normalize afterwards.

## Raw clips on the timeline with speed (verified 2026-11-16)
- Use the original raw clips trimmed (AppendToTimeline startFrame/endFrame in the clip's FPS as Resolve reports it; iPhone VFR clips show averages like 38 or 55) instead of pre-rendered time-lapse files, when the user wants to adjust lengths himself.
- Time-lapse: `item.SetSpeed({"Percentage": p})` keeps the timeline length and stretches the source range. Place the item at its slot length first (normal speed), then set p = source seconds x 30 / slot frames x 100. Build left to right (a placement overwrites to the right). 120 fps clips at 100% play in real time on a 30 fps timeline. Speeds over 30000% worked (375x).
- `MediaPool.DeleteTimelines([timeline])` removes scratch timelines; `Timeline.DuplicateTimeline(name)` copies one including markers.
