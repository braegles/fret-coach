# Changelog

Newest first. Each entry: version · who · what changed · what's left · notes for the other Claude.

## v38 — 2026-10-21 · Braedon's Claude (branch braedon/hide-chords)
- **Only the original 8 basic chords are shown for now** (Braedon: polish the basics first). 7ths, barre and power
  chords are hidden on the Chords screen and in the Changes loop picker. The group tabs are hidden too, since there's only one group.
- Hidden, not deleted: `ALL_GROUPS` still has all 29 chords, and the barre tips and detection slack stay in the code.
  `HIDDEN_GROUPS = ["7ths","Barre","Power"]` controls it, so bringing a group back means taking it off that list.
- A saved Changes loop that used a hidden chord falls back to G → C. Habits and "Practice my weak spot" skip hidden chords.
- Group tabs stretch to fill the row, and disappear when only one group is shown.
- No detection logic changed. Tested in a browser: tabs, Changes options, saved-loop fallback, habits filtering,
  intro flow. No script errors.

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
- Rebased onto main after v35 by Braedon's Claude on 2026-10-21 (David OK'd it). Only the version line and this
  changelog clashed, nothing in the cleanup itself changed.
- Version: tagged v36 because Braedon's PWA PR (#14) is v35. **Merge #14 first**, then this one (it may need a quick
  rebase on the version line).
- @Braedon's Claude: the `long` class in `setSay()` is untouched. `speak()` is gone; use `pause(ms)` if you need a wait.

## Docs — 2026-10-17 · Braedon's Claude (Issue #4, branch braedon/product-brief)
- Added PRODUCT.md: draft one-page product brief. It positions the app as a guitar practice coach (not just chords),
  with three core features: diagnose + fix, voice coach, and a daily workout that keeps you motivated. Free/Pro split and price included.
  Braedon's decisions: audience = beginners of any age + people who struggle to stay motivated; $4.99/month or
  $29.99 founder lifetime; voice coach is free. The name is still open (#5).
- CLAUDE.md §7 updated: new naming direction (practice/coach, not fret/chord; see Issue #5).
- No app changes, so no version bump.

## v35 — 2026-10-17 · Braedon's Claude (Issue #3, branch braedon/pwa)
- The app is now a PWA (progressive web app): it can be added to the iPhone home screen, opens full-screen
  with its own icon, and works offline after the first visit.
- New files: manifest.json (app name, colors, icons), sw.js (service worker), icons/ (192, 512, apple-touch 180).
- index.html: manifest + icon + iPhone home-screen tags in <head>; registers sw.js at the end of the script;
  the top/bottom padding respects the iPhone notch/status bar in full-screen mode.
- sw.js loads pages "network first": new versions show up right away when online, saved copy when offline.
  Each page is saved under its own address (fix suggested by David's Claude, so a second page like the camera
  lab can't overwrite the app's offline copy). Icons and Google Fonts are "cache first". If you add new app files, list them in SHELL in sw.js
  and bump CACHE.
- No detection logic changed. Tested in a browser served from a /fret-coach/ subfolder like GitHub Pages:
  service worker activates, all files cached, the app loads with the network turned off. No script errors.
  Not tested yet: Add to Home Screen + mic permission on a real iPhone.

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
