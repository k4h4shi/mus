# mus v0 Semantics

## 1. Time Base

- The master timeline is musical time.
- Beats per bar are derived from `time_signature` numerator.
- Clip duration in beats is `clip.length_bars * beats_per_bar`.

## 2. Quantization Rules

- Scene launch quantization is fixed to `1 bar` in v0.
- Hot-swap patch activation is also quantized to `1 bar`.
- Any request made between two boundaries is queued for the next boundary.

## 3. Launch Resolution

Given multiple `launch(scene)` requests in the same open bar window:

- Only the most recent request is committed at boundary.

This guarantees deterministic behavior during live coding.

## 4. Clip Playback

- Clips loop indefinitely while selected by the active scene.
- If clips in one scene have different lengths, each clip loops on its own cycle.
- The global pattern repeats at the least common multiple of active clip lengths.

## 5. Event Ordering and Collision

Within a clip, events are sorted by:

1. `at` ascending
2. declaration order ascending

Collision rule in a single track:

- same `at` and same pitch -> last declaration wins.

No collision normalization is applied across different tracks.

## 6. Validation Rules

A compiler MUST reject with errors when:

- duplicate track ids in a session
- duplicate clip ids in a track
- duplicate scene ids in a session
- scene references unknown track or clip
- clip length is not a positive integer bar count
- event onset is outside clip range
- velocity is outside `1..127`
- gate is outside `0.0..1.0`
- probability is outside `0.0..1.0`
- MIDI channel is outside `1..16`

## 7. Defaults

- velocity default: `100`
- gate default: `0.95`
- probability default: `1.0`
- track channel default: implementation-defined (SHOULD be stable per track)

## 8. MIDI Export Mapping

Normative mapping:

- `pitch` -> MIDI note number
- `velocity` -> NOTE_ON velocity
- note-off time -> `at + duration * gate`
- `duration` used only to derive NOTE_OFF scheduling

Probability handling:

- `prob < 1.0` MAY be realized at render time with deterministic seeded RNG.
- Export implementations SHOULD expose seed control for reproducibility.

## 9. MIDI Clock Synchronization

- Clock input can adjust transport tempo.
- Tempo changes MUST NOT force mid-bar scene re-quantization.
- New tempo becomes effective from the next bar boundary.

## 10. Hot Swap Contract

`hotSwap(prevIR, newSource)` behavior:

- parse and validate `newSource`
- if invalid: keep `prevIR` active and report diagnostics
- if valid: create `PatchPlan`
- apply patch atomically at next bar boundary

Patch application MUST be glitch-minimized and state-consistent.
