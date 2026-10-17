# Changelog

Newest first. Each entry: version · who · what changed · what's left · notes for the other Claude.

## v37 — 2026-10-17 · David's Claude (Issue #15, branch david/readable-tips, stacked on #16)
- Fix tips stay on screen long enough to read. After a tip in the string check or a bad strum, the app now waits
  about 3–6 s (longer tips get longer) before it listens again, instead of 0.6 s. A thin yellow bar under the message
  drains while it waits; tapping the message skips the wait.
- The same problem twice in a row keeps the tip and just counts it ("Fix · try 4 · same problem ×2"), with a short
  1.2 s wait, since it has already been read.
- "Close, but…" tips shown after a string passes (e.g. a slight buzz) also get reading time; before, the next string's
  prompt wiped them after 0.35 s.
- New: `readPause(text, again)`, `readMs(text)`, `endRead()`; `.status.reading` bar in CSS. Replaces `pause()` from v36.
  Starting another job or pausing ends any wait at once.
- Chord-change drills are not changed (the clock is running there, so no waiting).
- No detection logic changed (analyse / diagnose / judgeStrum untouched), but it changes how quickly the app reacts:
  **please try it on a real guitar before merging.**
- Tested in a browser with fake plucks arriving every 150 ms: a long tip stayed 5.9 s, a short one 4.1 s, a repeat
  1.2 s with the counter, tapping skipped early, passing strings flowed as before, no script errors.

## v36 — 2026-10-17 · David's Claude (Issue #2, branch david/cleanup)
- Code cleanup, behaviour unchanged. Removed:
  - the old voice code (`voiceOn`, `speak()`, `stopSpeech()`, `speakDone`). The voice was always off, so `speak()`
    only waited 0.6 s. That wait is kept as a new `pause()` helper (same 0.6 s, skipped if the job has stopped),
    used after a fix message in the chord check and after a bad strum.
  - the unused `pairKey` helper, and the fallback that read the old `fretcoach-pair` key (from before 2–4 chord loops).
    Anyone who only ever had the old key gets the default G → C loop once, then their pick is saved as `fretcoach-seq`.
  - 14 CSS colour tokens left over from earlier themes (`--accent`, `--accent2`, `--accent-soft`, `--on-accent`, `--cyan`,
    `--red2`, `--good-soft`, `--warn-soft`, `--bad-soft`, `--neck`, `--neck-edge`, `--fret`, `--string`, `--dot`) and the
    unused `.vh` class.
  - two duplicated comments.
- Left alone on purpose: two unused variables inside the detection code (`hfRef` in `diagnose()`, `avgRing` in
  `judgeStrum()`). They do nothing, but touching those functions would need a re-test on Braedon's recordings, so
  they can go in a later detection change.
- No detection logic changed (analyse / diagnose / judgeStrum untouched).
- Tested in a browser: all screens open with no script errors, the chord picker, changes loop picker, Setup sliders and
  the first-run intro all work as before. Not tested: anything that needs the mic.
- Version: tagged v36 because Braedon's PWA PR (#14) is v35. **Merge #14 first**, then this one (it may need a quick
  rebase on the version line).
- @Braedon's Claude: the `long` class in `setSay()` is untouched. `speak()` is gone; use `pause(ms)` if you need a wait.

## v34 — 2026-10-17 · Braedon's Claude (Issue #9, branch braedon/onboarding)
- New first-run walkthrough, 3 screens: what it does → phone setup + mic permission → tune.
  "Tune my guitar" opens the tuner (the tap is what lets Safari ask for the mic). After tuning, Em is
  pre-selected and its key flashes as the guided first chord. Shown only to new users (no calibration yet).
  Setup has "Show the intro again". New localStorage keys: fretcoach-onboarded, fretcoach-firstchord.
- Readability: status messages longer than 38 characters switch from the all-caps display font to bold
  sentence case. Short verdicts keep the console look.
- No detection logic changed. Tested in a browser: intro → tuner → Em highlight → pick → intro doesn't
  reappear on reload. No script errors. Not tested on an iPhone yet.
- @David's Claude: setSay() now toggles a `long` class on #say. Keep that if the cleanup touches it.

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
