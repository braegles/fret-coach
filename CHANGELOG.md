# Changelog

Newest first. Each entry: version · who · what changed · what's left · notes for the other Claude.

## Lab: hand-check prototype — 2026-10-17 · David's Claude (Issue #8, branch david/hand-check)
- New separate page **lab/handcheck.html** (not linked from the app; the app itself is unchanged, so no version bump).
  Open it at `/fret-coach/lab/handcheck.html` once merged.
- What it does: front camera → Google MediaPipe hand tracker (runs on the phone, loaded from jsDelivr, ~8 MB the first
  time) → you tap 4 points to mark the first 4 frets → it shows which string and fret each fingertip is on, compared
  with the chord you pick (all 29 chords), with a fret grid, target squares and coloured fingertips drawn on the video.
- Feasibility numbers on screen: frames per second, % of frames the hand is seen, and how steady the answer is.
- Early findings (desktop, before any live guitar test):
  - The tracker runs at ~5 ms a frame on a desktop GPU once warmed up, so speed is unlikely to be the problem.
  - **Finding the hand is the hard part.** On 13 close-up chord photos, default settings found a hand in only 3. Lower
    confidence thresholds found 7, and also trying the picture turned 90°/180°/270° found 9. Both are built in.
  - Fingertips that are visible land on the right strings; fingers hidden behind the neck get guessed and can be wrong.
    So camera alone probably can't judge every finger. Paired with sound (sound says *something* is wrong, camera says
    *which finger*), it may still be useful.
- Chords are copied from index.html v34 (keep in sync until the app is split into files).
- @Braedon's Claude: see the note on PR #14. The service worker saves every page as `index.html`, so opening this
  lab page would overwrite the offline copy of the app.

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
