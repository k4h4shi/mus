# mus v0 Language Specification

## 1. Purpose

`mus` is a declarative DSL for loop-based composition and live arrangement.
The language models music using `Clip / Track / Scene` and guarantees predictable behavior by quantizing live updates to bar boundaries.

## 2. Normative Keywords

The key words MUST, MUST NOT, SHOULD, and MAY in this document are to be interpreted as normative requirements.

## 3. Core Entities

- `session`: top-level container with global timing and all definitions.
- `track`: lane that owns clips and an optional MIDI channel.
- `clip`: reusable loop unit with fixed bar length and note events.
- `scene`: simultaneous clip selection across tracks.
- `launch`: request to activate a scene.

## 4. Global Defaults

If omitted, the implementation MUST apply these defaults:

- `bpm = 120`
- `time_signature = 4/4`
- `swing = 0.0`
- `launch_quantization = 1bar`
- `concert_pitch = A4(440Hz)`

## 5. Source File Model

- File extension SHOULD be `.mus`.
- One source file defines exactly one `session`.
- A session MUST contain at least one `track`, one `scene`, and one `launch`.

## 6. Track and Clip Model

- Track identifiers MUST be unique within a session.
- Clip identifiers MUST be unique within a track.
- A clip MUST declare `length` as a positive integer number of bars.
- Clip length SHOULD be one of `1,2,4,8`, but any positive integer is valid in v0.

## 7. Event Model

Each note event has:

- onset (`at`, in beats from clip start)
- pitch (`C4`, `F#3`, `Bb2`, etc.)
- duration (in beats, rational allowed)
- optional velocity (`1..127`, default `100`)
- optional gate (`0.0..1.0`, default `0.95`)
- optional probability (`0.0..1.0`, default `1.0`)

Event onset MUST satisfy:

- `0 <= at < clip_length_in_beats`

## 8. Scene Model

- Scene identifiers MUST be unique within a session.
- Scene bindings map `track_id -> clip_id`.
- A binding MUST reference an existing track and clip.
- Unbound tracks in a scene are interpreted as silent.

## 9. Live Update and Launch Semantics

- `launch(scene_id)` MUST take effect at the next bar boundary.
- Hot-swap patch application MUST take effect at the next bar boundary.
- If multiple launch requests arrive within one bar, the latest request wins.

## 10. Conflict Resolution

Within the same track, same onset, same pitch:

- the later event declaration MUST override the earlier one.

Events on different tracks are independent.

## 11. Interoperability Requirements

v0 implementation MUST define contracts for:

- MIDI file export (`.mid`)
- MIDI clock synchronization input and scheduling behavior

## 12. Compiler Contract

A conforming compiler MUST provide:

- `compile(source: string) -> SessionIR`

And SHOULD provide:

- structured diagnostics (`code`, `message`, `line`, `column`)

## 13. Non-goals for v0

- built-in DSP/waveform synthesis
- plugin-host runtime packaging
- immediate (sub-bar) scene switch mode
- standardized crossfade behavior
