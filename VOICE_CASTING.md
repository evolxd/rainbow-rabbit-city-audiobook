# Rainbow Rabbit City — Voice Casting Reference

For consistency across future books. All voices are ElevenLabs **premade** (official) catalog only — avoid the shared/community voice library, which caused an acoustic mismatch (different recording environment than the premade voices) in early drafts of Book One.

**Model:** `eleven_multilingual_v2`
**API:** raw `POST /v1/text-to-speech/{voice_id}`, default voice_settings unless noted.

## Cast

| Character | Species | ElevenLabs Voice | Voice ID | Rabbit pitch? |
|---|---|---|---|---|
| Narrator | — | George - Warm, Captivating Storyteller | `JBFqnCBsd6RMkjVDRZzb` | No |
| Leo | Human boy | Will - Relaxed Optimist | `bIHbv24MWmeRgasZH58o` | No |
| Fred | Red rabbit | Harry - Fierce Warrior | `SOYHLrjzK2X1ezoPC6cr` | **Yes** |
| George (bus driver) | Species unconfirmed | Chris - Charming, Down-to-Earth | `iP95p4xoKVk53GoZ742B` | No |
| Logan | Yellow rabbit | Liam - Energetic, Social Media Creator | `TX3LPaxmHKxFdv7VOQHJ` | **Yes** |
| Iris | Blue-green ice rabbit | Sarah - Mature, Reassuring, Confident | `EXAVITQu4vr4xnSDxMaL` | **Yes** |
| Sarah | Snow-white rabbit | Jessica - Playful, Bright, Warm | `cgSgspJ2msm6clMCkdW9` | **Yes** |
| Peter | Green rabbit | River - Relaxed, Neutral, Informative | `SAz9YHcvj6GT2YYXdXww` | **Yes** |
| Lily | Pink rabbit | Lily - Velvety Actress | `pFZP5JQG7iQjIQuC4Bku` | **Yes** |
| Lulu | Purple rabbit | Matilda - Knowledgeable, Professional | `XrExE9yKIg1WjnnlVkGX` | **Yes** |
| Luna | Golden rabbit | Alice - Clear, Engaging Educator | `Xb7hH8MSUJpSbSDYk0k2` | **Yes** |

## Rabbit pitch-shift (post-processing)

Applied only to the 8 confirmed-rabbit characters above (not Leo, not George — their species/humanity was left as originally written; see "Open questions" below).

```
ffmpeg -i in.mp3 -af "rubberband=pitch=1.12246:tempo=1:pitchq=consistency" -c:a libmp3lame -q:a 2 out.mp3
```

- `pitch=1.12246` = +2 semitones (2^(2/12))
- `pitchq=consistency` was chosen specifically because it fixed a warble/vibrato artifact that the default and `smoothing=on` modes didn't (smoothing fixed the warble but added audible hiss)
- Do **not** use `smoothing=on` — introduces a "沙沙" noise artifact
- A separate attempt at whole-book `loudnorm` (loudness normalization) plus an extra +3dB boost on George introduced an unacceptable noise artifact and was reverted — **don't repeat that exact approach** (`loudnorm=I=-19:TP=-2:LRA=7` on already pitch-shifted/re-encoded mp3s). If loudness consistency is revisited, test on one chapter first and listen carefully before running it across the whole book.

## Open questions for Book Two (young author to confirm)

- **Leo's species**: the document says "human boy," but the young author said in feedback that everyone is a rabbit, including Leo. Not yet resolved in Book One's audio — needs the young author to confirm before Book Two casting.
- **George's role**: young author feels George should read as more of a protagonist. Text/line count wasn't changed (that's the young author's call per the project's own rules), but audio presence (mix level, delivery) was the intended lever — not yet successfully executed due to the noise issue above.
- Luna's voice was picked without a preview round (minor role, 2 lines in Book One) — worth a proper preview pass if she has more lines in Book Two.

## Process notes (what worked)

1. Always preview a signature line per candidate voice before generating full chapters — full-book regeneration after an unapproved choice wasted real time and API cost more than once.
2. Check `category` field via `GET /v1/voices/{id}` before committing to a voice — `premade` = official studio quality, anything else (`professional`, etc., from `/v1/shared-voices`) risks inconsistent recording quality against the premade voices already in use.
3. Elevenlabs TTS is not fully deterministic between calls with identical text/voice/settings — use a fixed `seed` parameter if you need to reproduce an exact previously-approved rendering.
