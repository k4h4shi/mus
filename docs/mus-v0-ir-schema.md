# mus v0 IR Schema

## 1. Purpose

`SessionIR` is the canonical compile output for scheduling, MIDI rendering, and live patching.

## 2. Top-Level Shape

```json
{
  "version": "0",
  "session": {
    "id": "minimal",
    "bpm": 120,
    "timeSignature": [4, 4],
    "swing": 0.0,
    "tracks": [],
    "scenes": [],
    "launch": { "sceneId": "intro", "quantization": "1bar" }
  }
}
```

## 3. Type Contracts

```ts
export type SessionIR = {
  version: "0";
  session: {
    id: string;
    bpm: number;
    timeSignature: [number, number];
    swing: number;
    tracks: TrackIR[];
    scenes: SceneIR[];
    launch: LaunchIR;
  };
};

export type TrackIR = {
  id: string;
  midiChannel?: number;
  clips: ClipIR[];
};

export type ClipIR = {
  id: string;
  lengthBars: number;
  lengthBeats: number;
  events: EventIR[];
};

export type EventIR = {
  atBeat: number;
  pitch: string;
  midiNote: number;
  durationBeat: number;
  velocity: number;
  gate: number;
  probability: number;
  order: number;
};

export type SceneIR = {
  id: string;
  launchQuantization: "1bar";
  clipBindings: ClipBindingIR[];
};

export type ClipBindingIR = {
  trackId: string;
  clipId: string;
};

export type LaunchIR = {
  sceneId: string;
  quantization: "1bar";
};
```

## 4. Invariants

- `version` MUST be `"0"` for v0.
- `timeSignature[0] > 0`, `timeSignature[1] > 0`.
- `lengthBars >= 1`.
- `lengthBeats = lengthBars * timeSignature[0]`.
- Every `clipBinding` MUST resolve to an existing track and clip.

## 5. Patch Plan Contract

```ts
export type PatchPlan = {
  effectiveAtBar: number;
  next: SessionIR;
  changes: {
    tracksChanged: string[];
    clipsChanged: Array<{ trackId: string; clipId: string }>;
    scenesChanged: string[];
  };
};
```

- `effectiveAtBar` is computed on active transport timeline.
- `next` is fully validated before patch creation.
