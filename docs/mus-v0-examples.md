# mus v0 Canonical Examples

## 1. Minimal Session

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

## 2. Groove Session (Two Scenes)

```mus
session groove {
  bpm 124
  time_signature 4/4

  track drums channel 10 {
    clip verse_beat length 1bar {
      notes {
        at 0.0 C1 1/4 vel 112;
        at 1.0 D1 1/8 vel 95;
        at 2.0 C1 1/4 vel 118;
        at 3.0 D1 1/8 vel 92;
      }
    }

    clip hook_beat length 1bar {
      notes {
        at 0.0 C1 1/4 vel 120;
        at 0.5 D1 1/8 vel 100;
        at 2.0 C1 1/4 vel 122;
        at 2.5 D1 1/8 vel 102;
      }
    }
  }

  track bass channel 1 {
    clip verse_bass length 2bar {
      notes {
        at 0.0 C2 1/2 vel 98;
        at 2.0 G1 1/2 vel 95;
        at 4.0 F1 1/2 vel 96;
        at 6.0 G1 1/2 vel 95;
      }
    }

    clip hook_bass length 2bar {
      notes {
        at 0.0 A1 1/2 vel 102;
        at 2.0 E1 1/2 vel 100;
        at 4.0 F1 1/2 vel 99;
        at 6.0 E1 1/2 vel 100;
      }
    }
  }

  scene verse {
    drums: verse_beat;
    bass: verse_bass;
  }

  scene hook {
    drums: hook_beat;
    bass: hook_bass;
  }

  launch verse;
}
```

## 3. Polyloop Session (Length Mismatch)

```mus
session polyloop {
  bpm 118
  time_signature 4/4

  track perc channel 10 {
    clip perc3 length 3bars {
      notes {
        at 0.0 C1 1/8 vel 105;
        at 1.5 D1 1/8 vel 92;
        at 4.0 C1 1/8 vel 108;
        at 7.0 D1 1/8 vel 90;
      }
    }
  }

  track synth channel 2 {
    clip arp4 length 4bars {
      notes {
        at 0.0 C4 1/8 vel 100;
        at 0.5 E4 1/8 vel 100;
        at 1.0 G4 1/8 vel 100;
        at 1.5 B4 1/8 vel 100;
      }
    }
  }

  scene a {
    perc: perc3;
    synth: arp4;
  }

  launch a;
}
```

Behavior note:

- `perc3` loops every 3 bars.
- `arp4` loops every 4 bars.
- Combined phase realigns every 12 bars (LCM of 3 and 4).
