# Transcription and captions

## Transcribing locally
- `transcribe.py <media> <out.json> [--model large-v3] [--language ur|en|auto]`: faster-whisper on the GPU (float16), beam 5, VAD filter, word timestamps. 45 s of audio takes 8 to 16 s including model load; a 25 s voiceover about 5 s.
- It decodes audio through FFmpeg first and imports torch before faster-whisper (PyAV 19 breaks the library's own loader; ctranslate2 needs the CUDA DLLs that torch brings).
- English-only speech: `--language en` is accurate, including word times to about 0.05 s. Transcribe voiceover files and the rendered draft the same way.
- Word times of long held syllables can be stretched; check a line's start by its first word, not by the segment start.

## Urdu and English mixed speech
- `--language auto` detected Hindi for a mixed clip, wrote Urdu words in Devanagari, and skipped a stretch. `--language ur` covers everything but writes Urdu script and spells English words phonetically in Urdu.
- **Decision: captions are Roman Urdu** (Urdu words in Latin letters, English words as written). Whisper does not output this directly: needs a transliteration step, another model, or a prompt (not built yet). Test several models on the user's own voice first.
- Captions only when the user asks, for the whole video or a part. English-only videos get English labels and no Roman Urdu conversion.

## Using transcripts
- Show lines with timeline timestamps so the user can decide cuts; never choose between complete takes automatically.
- For placing graphics and sounds use word times from the transcript of the rendered draft. The user's times are DaVinci timecodes (SS:FF), convert before comparing.
- Lines that Whisper wrote differently from what was said (for example "GTS" for "GTA", "knife in duo" for "new iPhone Duo") are transcription errors; the user's words win for on-screen text.
