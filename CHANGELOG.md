# Changelog

Newest first. Each entry: version · who · what changed · what's left · notes for the other Claude.

## v33 — 2026-10-17 · David's Claude (PR #1)
- Fix: the "Muted below" slider in Setup now remembers its setting. The code that loads the saved value
  (`fretcoach-muteat`) was on the same line as a `//` comment, so it never ran and the slider reset to 50%
  on every launch. Moved it onto its own line. Version tag bumped to v33.
- No detection logic changed (analyse / diagnose / judgeStrum untouched).
- Tested in a browser: set the slider to 30%, reloaded, it stayed at 30% (v32 snapped back to 50%).
  All screens except the Tuner open with no script errors. Not tested: anything that needs the mic.
- What's left: nothing for this fix. @Braedon's Claude: David has seen the Issues; he'll claim #2 (cleanup)
  before starting it, but not yet.

## Repo setup — 2026-10-17 · Braedon's Claude
- Added CLAUDE.md (shared context + plan + collaboration rules), CHANGELOG.md, README.md.
- Opened starter Issues from the plan (see GitHub Issues).
- @David's Claude: start by reading CLAUDE.md, then pick an Issue. Code cleanup (Issue #2) is a good first task.

## v32 — 2026-10-15 · Braedon's Claude
- Barre chords are less strict. A buzz only counts when there's real 2–5 kHz energy; barre strings get more
  slack on ring time; buzz thresholds are higher for barre chords.
- Tested on Braedon's F and Bm recordings (false flags dropped from 10 to 3). Real-buzz recordings are still caught 5/5.

## v31 — 2026-10-15 · Braedon's Claude
- 29 chords in 4 groups (Basic / 7ths / Barre / Power) with group tabs on the Chords screen.
- Barre drawn as one bar on the diagram; diagram shifts up the neck for high shapes (e.g. G5).
- Barre-specific tips. Strum check fixed for octave-doubled notes (F, Bm strums now pass).
- Chord changes: loops of 2–4 chords, per-loop BPM, slowest change shown.

## v1–v30 — before the repo
- Built in one long chat: string-by-string check, strum check, tuner + calibration, Y2K console UI,
  menu screens, chord change trainer, Habits mistake profile. See CLAUDE.md Part 2 for how it all works.
