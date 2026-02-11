# mus

This project is currently in planning/specification stage.
Implementation has not started yet, and development is planned next.

`mus` is a loop-based programming language for making music.
It is designed for programmers who build tracks from clips and perform section changes live, similar to a clip/track/scene workflow.

## Status

- Phase: v0 specification draft
- Implementation: not started
- Current scope: language spec, semantics, IR contract, examples

## Design Principles

- Loop-first composition with `Clip / Track / Scene`
- Predictable live behavior through bar-quantized updates
- Declarative syntax with strong compile-time validation
- DAW interoperability via MIDI export and MIDI clock sync

## v0 Scope

- Language syntax and compile-time rules
- Session/Track/Clip/Scene data model
- Hot-swap semantics (apply on next bar)
- MIDI export contract
- MIDI clock synchronization contract

Out of scope for v0:

- Built-in audio synthesis engine
- VST/AU plugin runtime
- Immediate scene switching and standard crossfade model
- GUI editor

## Example

```mus
session minimal {
  bpm 120
  time_signature 4/4

  track drums channel 10 {
    clip kick length 1bar {
      notes {
        at 0.0 C1 1/4 vel 112 gate 0.95;
        at 1.0 C1 1/4 vel 100 gate 0.90;
        at 2.0 C1 1/4 vel 118 gate 0.95;
        at 3.0 C1 1/4 vel 102 gate 0.90;
      }
    }
  }

  scene intro {
    drums: kick;
  }

  launch intro;
}
```

## Repository Layout

- `docs/mus-v0-language-spec.md`: normative language specification
- `docs/mus-v0-grammar.ebnf`: v0 grammar definition
- `docs/mus-v0-semantics.md`: runtime and compile-time behavior rules
- `docs/mus-v0-ir-schema.md`: IR contracts and JSON shape
- `docs/mus-v0-examples.md`: canonical examples

## Planned Interfaces (v0)

- `compile(source: string) -> SessionIR`
- `renderMidi(ir: SessionIR, sectionPlan) -> .mid`
- `syncClock(ir: SessionIR, clockIn) -> schedule`
- `hotSwap(prevIR, nextSource) -> PatchPlan`

## License

MIT License. See `LICENSE`.
