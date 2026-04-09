# Bass Practice — Feature Spec

A catalog of practice modes and assist features to consider beyond the
current scale / box-pattern drill.

## 1. What we have today

- **Note targeting** — random or sequential notes within a scale and a
  position window. Match by exact MIDI. Hit/miss with reaction time.
- **Metronome (beat-driven)** — click track that advances the target on
  each beat.
- **Recording** — capture played notes onto a piano roll, with playback
  through a simple bass synth and a draggable loop region.
- **Tuner** — pitch + cents.
- **Session stats** — hits, misses, average reaction, minimap of the run.

## 2. Why expand

Different things benefit from different drills, and the app currently
trains a small slice of "find this note". The features below are
organized by **what they actually train**, with concrete proposals and
honest effort estimates.

The four pillars worth training early on bass:

1. **Note knowledge** — fretboard memorization, intervals (mostly done).
2. **Time** — playing in the pocket; this is what most beginners lack.
3. **Chord-tone vocabulary** — what notes belong to a chord, where they
   sit; the basis of every bass line.
4. **Tone production** — pluck consistency, sustain, no fret noise.

## 3. Note knowledge & fretboard fluency

Already partially covered. Extensions worth considering:

### 3.1 Note name quiz
- Show a note name (no fretboard hint); user plays it anywhere.
- Or: show a position on the fretboard with no name; user names it (via
  a click) and plays it.
- Or: show on a written staff (bass clef). This is sight-reading practice.
- Hit/miss + reaction time; great for spaced-repetition memorization.

### 3.2 Interval calling
- Show an interval ("minor 3rd up from C"); user plays the answer note.
- Trains interval recall vs. fretboard.

### 3.3 Position shifts
- Show two notes in different positions; user plays first then second
  with a smooth transition. Could measure the time gap and the
  intonation of the slide if done legato.

## 4. Chord-tone & arpeggio drills

This covers your "root → fifth → octave" idea, generalized.

### 4.1 Arpeggio sequencer
- Pick a chord type: maj, min, 7, maj7, m7, dim, m7b5, etc.
- Pick a key — or "all 12 keys" with a cycling order:
  - Chromatic (C, C#, D, ...)
  - Circle of fifths (C, G, D, A, ...)
  - Circle of fourths (C, F, Bb, Eb, ...)
  - Random
- For each root, target the chord tones in order:
  R → 3 → 5 → 7 → R↑ → 7 → 5 → 3.
- Two modes: hit-to-advance (current behaviour) or beat-driven (new).

### 4.2 R-5-8 walking primer
- The simplest bass-line foundation. Sequence:
  R → 5 → R↑ → 5 → R → ...
- Variants: R → 5 → 6 → b7 → R↑ (rock'n'roll boogie line),
  R → 3 → 5 → 6 (walking quarter notes).
- Always tied to a metronome. Nothing trains time-with-melody better.

### 4.3 12-bar blues form
- Cycles you through a standard 12-bar blues in your chosen key.
- Each bar of the form has a target chord (I7, IV7, V7) and you play
  chord tones for that bar.
- Trains the most-used progression in popular music + chord-following.

### 4.4 Common pop / jazz progressions
- Library of progressions: I–IV–V, I–V–vi–IV, ii–V–I, vi–IV–I–V,
  Andalusian (i–VII–VI–V), etc.
- For each chord change, surface chord-tone targets.
- Cycle the progression in time. Like 4.3 but generalized.

## 5. Rhythm & time

The current metronome only advances targets — it never lets you
practice timing per se. This is the biggest gap in the app.

### 5.1 Free-time metronome
- Click runs at the chosen BPM. Targets *don't* change with the click.
- User just plays whatever they want in time, and we measure timing
  accuracy of each pluck relative to the nearest beat.
- Output: a histogram of "ms early / ms late" — rushing vs. dragging.

### 5.2 Subdivision drills
- Target "play one note per beat", "two per beat" (eighths), "three"
  (triplets), "four" (sixteenths).
- Click sounds on beat 1 only (or every quarter); user plays the
  subdivision in between.
- Visual: tick marks on the timeline at expected pluck times; actual
  plucks rendered as offsets from those marks.

### 5.3 Drop-out / silent bars
- Click for N bars, silent for M, then click again.
- Trains internal time-keeping. We can measure how much you've drifted
  by the moment the click resumes.

### 5.4 Click on 2 and 4 (jazz convention)
- Click only on beats 2 and 4; you feel beats 1 and 3.
- Stronger time discipline than click-every-beat. One toggle.

### 5.5 Tempo ramping
- Start at BPM X, ramp to BPM Y over the session.
- Useful for building speed without losing accuracy. Can be combined
  with any practice drill.

### 5.6 Per-note timing scoring
- Even in current drills, record the **timing offset** of each hit
  relative to the nearest beat. Already 80% there — we have hit
  timestamps and a metronome tick array — we just don't compare them
  yet.

## 6. Groove & feel

Less about correctness, more about "does it sound good".

### 6.1 Backing track playback
- A drum loop (kick, snare, hat) and optionally a chord progression
  played in sync with the practice clock.
- Drum loops can be Web-Audio-synthesized (cheap) or sample-based
  (better but needs files).
- Chord progressions auto-generated from a pattern library.

### 6.2 Genre patterns
- Library of grooves: rock 8ths, funk 16ths, reggae one-drop, motown
  walking, jazz comping, latin tumbao.
- Each preset sets BPM, drum pattern, and recommended scale shape.

### 6.3 Call & response
- App plays a 1-bar pattern via the synth.
- User copies it in the next bar.
- Tests ear + recall. Score by note correctness *and* timing similarity.

## 7. Tone quality (most ambitious, most differentiated)

Pitch detection alone doesn't tell us much about playing quality. The
audio signal carries a lot more — most of which we're already capturing
in the analyser buffer and discarding. Things we *could* extract:

### 7.1 Attack distinctness
- For each note, measure attack time (silence → peak amplitude).
- Sharp pluck = short attack (<20 ms); muddy = longer (~80 ms).
- Graph attack time across the session — consistency is the goal.

### 7.2 Note separation
- Measure the silence gap between consecutive notes.
- Staccato drill: target a specific gap (e.g. 100 ms).
- Legato drill: target near-zero gap.

### 7.3 Dynamic consistency
- RMS per note; standard deviation across the session.
- "Did you play 30 notes at the same volume?" — drives even, consistent
  attack.

### 7.4 Buzz / fret noise detection
- A buzzing note has unusual high-frequency content — spectral centroid
  shifted up relative to the fundamental.
- Implementation: AnalyserNode `getFloatFrequencyData` → ratio of
  energy above ~2× fundamental vs. below.
- Hard to make perfectly accurate, but a "buzz score" per note would
  flag bad ones for review.

### 7.5 Sustain length
- How long does the note ring before decaying below threshold?
- Indicates left-hand finger pressure and right-hand technique.
- Target: "play a note that rings for at least 2 seconds at every
  fret in this position".

### 7.6 Pitch stability
- We already detect cents offset. Across the duration of a held note,
  the cents value should be approximately stable. Wobble = inconsistent
  finger pressure.
- "Cents drift per note" as a quality metric — easy to add.

### 7.7 Difficulty tier
| Metric | Effort | Reliability |
|---|---|---|
| 7.6 Pitch stability | Trivial | High — already computing cents |
| 7.3 Dynamic consistency | Small | High — RMS already there |
| 7.1 Attack time | Small | Medium — needs onset detection |
| 7.2 Note separation | Small | Medium — needs onset detection |
| 7.5 Sustain length | Small | Medium — needs offset detection |
| 7.4 Buzz detection | Medium | Lower — heuristic, false positives |

## 8. Ear training

### 8.1 Pitch identification
- App plays a note via the synth; user plays the same pitch back.
- Score by pitch class (loose) or exact MIDI (strict).

### 8.2 Interval identification
- Play two notes; user plays the same interval starting from a different
  root.
- Or: identify the interval name from a list.

### 8.3 Chord quality identification
- Play a triad/seventh chord (synth arpeggiates or plays simultaneously);
  user identifies major / minor / dim / aug / 7 / m7 / maj7.

## 9. Repertoire / spaced repetition

### 9.1 Lick library
- User records a short phrase, names it, files it.
- App schedules reviews ("play the funk slap riff #3 — last seen 4 days
  ago").
- Standard spaced-repetition scheduling (Leitner / SM-2).

### 9.2 Section-by-section practice
- Import or paste a tab; app cycles you through small sections,
  prioritizing the ones you struggle with based on past hit rate.

## 10. Suggested cluster of notes / "vocabulary" feature

You asked specifically about "clusters of notes that need to be known in
common keys that cover a lot of songs". Here's a concrete proposal:

### 10.1 Vocabulary packs
A small library of pre-defined note clusters tied to musical contexts.
For each pack, you get a position window plus a small set of "important"
notes within it. Practice mode targets only those notes.

Examples:

- **"Walking the I"** — root, 3rd, 5th, 6th of the I chord in 5
  positions across 12 keys.
- **"12-bar blues vocab in E"** — root/3/5/b7 of E7, A7, B7 in their
  natural positions.
- **"Pop bass essentials"** — roots of C, G, D, A, E, F, Bb, Em, Am, Dm
  in their lowest available positions.
- **"Funk box in A minor"** — A minor pentatonic positions on the E and
  A strings, plus the b5 chromatic passing tone.
- **"Modal vamps"** — Dorian on D (D-Dorian = D minor with a natural 6),
  Mixolydian on G, etc.

Each pack is just data: `{ name, root, scale, position, allowedScaleDegrees }`.
The drill is the existing practice loop, but only the listed degrees
are picked as targets.

This is **the cheapest big win** in the spec — it's purely a content
library on top of code we already have.

## 11. Implementation priorities

### Easy wins (small extensions of current code)

| Feature                          | Section | Effort | Why valuable                              |
|----------------------------------|---------|--------|-------------------------------------------|
| Per-note timing scoring          | 5.6     | XS     | We already record beat times + hits       |
| Click on 2 & 4                   | 5.4     | XS     | One toggle                                |
| Free-time metronome              | 5.1     | S      | Foundational; histogram of ms-off         |
| Tempo ramping                    | 5.5     | S      | One slider; combinable with anything      |
| Pitch stability metric           | 7.6     | S      | Reuses existing cents detection           |
| R-5-8 walking primer             | 4.2     | S      | Tiny new sequence type                    |
| Vocabulary packs                 | 10.1    | S      | Pure data on existing engine              |
| Note name quiz                   | 3.1     | S      | UI tweak on current targeting             |
| Dynamic consistency metric       | 7.3     | S      | RMS already computed                      |

### Medium

| Feature                          | Section | Effort |
|----------------------------------|---------|--------|
| Arpeggio sequencer               | 4.1     | M      |
| 12-bar blues form                | 4.3     | M      |
| Common progressions library      | 4.4     | M      |
| Subdivision drills               | 5.2     | M      |
| Drop-out bars                    | 5.3     | M      |
| Attack time / note onset         | 7.1     | M      |
| Sustain length                   | 7.5     | M      |
| Pitch identification ear drill   | 8.1     | M      |
| Call & response                  | 6.3     | M      |

### Larger

| Feature                          | Section | Effort |
|----------------------------------|---------|--------|
| Backing tracks (drum loops)      | 6.1     | L      |
| Genre groove library             | 6.2     | L      |
| Buzz detection                   | 7.4     | L      |
| Lick library + spaced repetition | 9       | L      |
| Bass-clef sight reading          | 3.1     | L      |
| Interval / chord ear drills      | 8.2/8.3 | L      |

## 12. Suggested next two iterations

If you want a concrete bite-sized roadmap:

### Iteration A — "Train the time"

Adds rhythm-focused drills which are arguably the biggest weakness most
beginners (and many intermediates) have. All small.

1. **5.6 Per-note timing scoring** — passively measure ms-offset of
   every hit relative to the nearest beat. Show a histogram in the
   session summary.
2. **5.1 Free-time metronome** — new mode where targets stay put and
   the metric is timing only.
3. **5.4 Click on 2 & 4** — toggle on the practice row.
4. **5.5 Tempo ramping** — optional slider that ramps BPM across the
   session.

### Iteration B — "Train the chord vocabulary"

Adds melodic/harmonic content. Builds on the existing target picker.

1. **4.2 R-5-8 walking primer** — new sequence type alongside random
   and sequential.
2. **10.1 Vocabulary packs** — a small library of preset
   `(scale, position, allowed degrees)` bundles, picked from a dropdown
   that overrides the current scale/position controls.
3. **4.1 Arpeggio sequencer** — pick chord type and a key-cycling
   strategy; targets the chord tones for each root in turn.

### Iteration C — "Train the tone"

Once the basics above are in place, add measured-quality feedback.
This is where the app can do something nothing else really does well.

1. **7.6 Pitch stability** — cents drift per held note.
2. **7.3 Dynamic consistency** — peak RMS per note + std deviation.
3. **7.1 Attack time** — basic onset detection from RMS envelope.
4. Show all three as small dial graphics in the session summary.

## 13. What I'd skip (or defer)

- **Sight reading on a staff** — high effort (rendering notation, parsing
  positions), and there are dedicated apps that already do this well.
- **Tab importing** — same; tab parsing is a project of its own.
- **Polyphonic detection** — bass is monophonic in 95% of practice
  contexts, and pitch detection of two simultaneous notes is hard.
- **Fancy DSP buzz detection** — heuristic at best, prone to false
  positives. Defer until the simpler tone metrics are in place.
