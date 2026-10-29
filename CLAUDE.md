# Fret Coach — CLAUDE.md (shared project brain)

Two people work on this repo, each with their own Claude: Braedon (owner, guitar tester, product) and David (code).
The two Claudes can't talk to each other directly. THIS REPO is how they stay in sync. Read this file first, every session.

## Collaboration protocol (both Claudes follow this)
1. Start of every session: `git pull`, then read CHANGELOG.md (top entries) and the open GitHub Issues
   (`gh api repos/braegles/fret-coach/issues`). Note anything the other Claude left for you.
2. Never work directly on `main` for anything bigger than a one-line fix. Make a branch named
   `braedon/<topic>` or `david/<topic>`, commit there, and open a pull request (PR) into `main`.
   Merging to `main` = it goes live on GitHub Pages, so only merge after it's been tested.
3. Commit messages say WHAT changed and WHY, in plain words.
4. Every merged change gets a CHANGELOG.md entry at the top: version, who, what changed, what's left,
   and any "@other-claude: please check X" notes.
5. Bump the version tag in index.html (`<span class="ver">vNN</span>`) on every release to main.
6. Claim work by assigning/commenting on an Issue before starting, so both sides don't build the same thing.
   Questions for the other side go in an Issue comment (or the PR), not just in chat.
7. Don't overwrite the other person's in-progress branch. If two PRs touch the same lines, the second one rebases.
8. If you change detection logic (analyse / diagnose / judgeStrum), say so loudly in the CHANGELOG. It needs
   re-testing on real guitar recordings before merging (Braedon has the recordings).
9. Keep this file up to date when the plan or architecture changes. It's the shared memory.

## Repo layout
- index.html: the whole app (HTML + CSS + JS in one file). This is what GitHub Pages serves. Edit it directly.
- CLAUDE.md: this file. Project context, plan, and rules.
- CHANGELOG.md: version history and handoff notes between the two Claudes.
- README.md: a short public description.
- PRODUCT.md: the one-page product brief (who it's for, promise, core features, price). Read it before
  proposing features.
- manifest.json, sw.js, icons/: make it an installable, offline-capable PWA. sw.js lists the app files to cache
  (SHELL) and has a CACHE version name. Bump it when that list changes.

- Live app: https://braegles.github.io/fret-coach/  (open on an iPhone in Safari, allow the microphone)
- Repo: https://github.com/braegles/fret-coach
- Deploy: merge to `main` (GitHub Pages serves index.html from main; republishes in about a minute).
  The version tag in the header (e.g. "V38") shows what's live. Reopen Safari if the old version sticks.
- Teamwork: Braedon adds David as a collaborator (repo Settings → Collaborators). Only one person edits
  index.html at a time, or use branches, so uploads don't overwrite each other.


---------------------------------------------------------------------------------------------------

# PART 1 — THE PLAN

## 1. What we have today
A working prototype that does something most guitar apps don't: it listens to each string of a chord and
names the finger causing the problem ("your ring finger is muting the D string, arch it"). It also has a tuner,
strum check, a chord-change trainer (2–4 chord loops with a speed target) and a "Your habits" mistake profile.
29 chords. Runs in the browser, so there's no App Store fee and no Mac needed yet.

Competitors (Yousician, Fender Play, Simply Guitar) listen too, but they mostly score whether you hit the
right notes. They don't diagnose your hand. Yousician and Fender Play are around €19.99/month.
That gap, plus a much lower price, is our opening.

## 2. Decide what the product is (Week 1 — do this first; everything else depends on it)
Working definition to test:
> "A pocket guitar teacher for your first 90 days. It hears every chord and tells you exactly which
> finger to fix, so you get past the stage where most beginners quit."

Decisions to make together:
- Who exactly: total beginners (adult, self-taught, phone-only) vs. kids with parents paying vs. guitar teachers'
  students. Recommendation: self-taught adult beginners first. They feel the pain, pay for themselves,
  and find apps through TikTok/YouTube.
- The core promise: "clean chords + smooth changes in 30 days", a promise we can measure with the app's own data.
- What we are NOT (for now): a song library, a lesson-video platform, or a tab app. Big competitors own those.
- The free vs. paid line (see section 6).
Deliverable: a one-page product brief (who, problem, promise, the 3 core features, the price).

## 3. The next "secret sauce" (Weeks 1–3 to pick, prototype after)
Our current edge is finger diagnosis by sound. Candidates for the second edge, ranked:

1. Camera + sound hand check (recommended to explore first)
   Prop the phone up and the front camera sees the fretting hand. Hand-tracking (e.g. Google MediaPipe Hands,
   which runs in the browser on-device) shows which finger is on the wrong string or fret BEFORE you strum,
   and sound confirms after. No beginner app does both. Risk: lighting/angles. Test with a quick prototype
   before committing.
2. Daily "fix my hands" workout
   The Habits data already knows your worst finger and slowest change. Generate a 5-minute daily routine from it
   (e.g. "3 min of G→C, 2 min of pinky arch drills"), with streaks. Cheap to build; drives daily use and retention.
3. "Play your song" mode
   Paste or pick any song's chord list. The app turns it into a change drill using that song's chords,
   speeding up toward the real tempo. Motivation is the song you love. No licensing needed for chord names alone.
   Avoid copying lyrics or tabs.

Plan: build #2 quickly (it reuses existing code), prototype #1 as the headline differentiator, and keep #3 as a
feature in the paid tier.

## 4. Clean up, compress and organize the app (Weeks 1–2, mostly David + Claude)
The app is one ~90 KB file built up over 32 versions. Before adding big features:
- Remove dead code: the old voice/speech code (voiceOn, speak, stopSpeech), the old pairKey helper and
  fretcoach-pair migration, and unused CSS from the earlier themes.
- Organize into clear sections (or separate files: styles.css, audio.js, analysis.js, ui.js) while keeping a
  one-file build for easy deploys.
- Make it a PWA (progressive web app: a website that installs to the home screen and works offline like an app):
  add a manifest + service worker + app icon. Big "real app" feel with zero App Store cost.
- Minify the deployed file (shrinks it for faster loading).
- Keep an offline test set: the owner's recordings + test scripts, so detection changes can be checked before shipping.
- Put the code in the repo properly (README, version history), not just index.html uploads.

## 5. UI tweaks (Weeks 2–4)
Keep the Y2K console identity (hard edges, chrome, LEDs; the owner rejected rounded "AI bubbly" looks), but:
- First-run onboarding: 3 screens covering what the app does, mic permission, and tuning. Then one guided first chord.
- Readability: the main status message is big ALL-CAPS display text. Use sentence case for long tips.
- Progress you can see: streaks, chords mastered, change-speed graph per chord pair.
- Clear "Pro" markers once there's a paid tier.
- iPad/landscape layout, larger tap targets during play, and test on small iPhones.
- Shareable result cards ("I hit G→C in 1.2 s") for social posts. That's free marketing.

## 6. Money: fastest realistic path (start in parallel with sections 4–5)
Honest take on ads: ad networks pay per thousand views, so a small new app with a few hundred users makes almost
nothing from ads. Ads also can't run while the mic is listening (they'd ruin the core experience).
Ads are a later, minor layer. Direct revenue comes first.

Ranked by speed:
1. Affiliate links (days to set up): a "Beginner gear we recommend" screen (strings, capo, tuner, beginner guitars)
   using Amazon Associates / Sweetwater-type affiliate programs. Beginners need gear, and this fits naturally.
2. Pro unlock on the web (1–3 weeks): free tier = basic chords + tuner + string check. Pro = barre/7ths/power chords,
   change trainer loops, habits workout, song mode. Charge through a Stripe Payment Link (no App Store, no 15–30% cut).
   Suggested launch price: about $4.99/month or about $29.99 "founder lifetime", far below competitors.
   Note: an unlock code checked only inside the page can be bypassed. That's fine for early adopters;
   add real accounts (e.g. Supabase or Firebase auth) once people are paying.
3. Short-form video marketing (start now, costs nothing): TikTok/Reels/Shorts of the app catching a muted string,
   before/after chord changes, "beginner tries for 30 days". This is how beginner guitar apps get found.
4. Guitar teachers and local music shops: a "homework checker" for students, sold per teacher. Fewer users,
   but higher price and word of mouth.
5. Later: native iPhone app with App Store subscriptions (Apple Small Business Program = 15% fee),
   then ads via AdMob on non-practice screens or "watch an ad to unlock a Pro chord for a day".

Needed before charging money: own domain (also needed for AdSense later), privacy policy + terms
(selling point: audio is analysed on the phone and never uploaded), and Stripe account.

## 7. New name (Week 1–2) — Issue #5
"Fret Coach" is a working name. Direction from Braedon: the name should be about **learning and practicing
guitar** (a practice/teacher app), with words like **coach**, not "fret" or "chord". Starter ideas and the
availability-check process live in Issue #5.
Rename only after the check, then buy the domain and move the site to it.

## 8. Timeline at a glance
- Week 1: product brief, name shortlist, start posting videos, cleanup begins, affiliate gear screen.
- Weeks 2–3: PWA + onboarding + UI fixes, pick secret sauce, build the daily workout, new name + domain.
- Weeks 3–5: Pro tier + Stripe, privacy policy, launch to friends/TikTok, collect feedback.
- Weeks 5–8: camera hand-check prototype, live-test all 29 chords, real accounts if people pay.
- Later: native iPhone app (needs a Mac + Xcode + $99/yr Apple developer account), App Store launch, ads layer.

## 9. Who does what (suggestion — adjust together)
- Braedon: product decisions, testing on the guitar, recordings, videos/marketing, name/brand, pricing.
- David: code cleanup, PWA, payments/accounts, camera prototype, repo organization.
- Both: weekly check-in on what users say, one numbered version per release.

## 10. Numbers to watch
- Activation: % of new users who complete their first chord.
- Retention: % who come back after 1 day and after 7 days.
- Conversion: % of active users who pay for Pro.
- Learning: average chord-change time going down (proof the product works, and great marketing).

---------------------------------------------------------------------------------------------------

# PART 2 — TECHNICAL HANDOFF

## How the file is laid out (single file: HTML + CSS + JS, no build step, no libraries)
- CSS: Y2K console look (chrome bevels, hard edges, Anton display font, green wireframe canvas background, LEDs).
- Screens via the top menu: Chords, Changes, Tuner, Habits, Setup (body[data-screen] CSS rules show/hide panels).
- JS sections, in order:
  - CHORDS library (29 chords): f = fret per string low E→high e (-1 don't play, 0 open), g = finger 1–4,
    optional barre {fret,from,to}. ALL_GROUPS = Basic / 7ths / Barre / Power; HIDDEN_GROUPS (7ths, Barre,
    Power hidden since v37: only the 8 basic chords show) are left out of GROUPS, which is what the UI shows. lab() = display names (B♭, F♯m).
  - drawBoard(): SVG chord diagram (fret window shifts up for high shapes; barre drawn as one bar).
  - Analysis (pure functions, testable offline in Node):
    - analyse(): one plucked string → note (FFT over the string's possible range, minus the pre-pluck spectrum),
      ring time (late/early harmonic ratio), brightness, buzz (spectral flatness 2–5 kHz).
    - diagnose(): compares a pluck to that string's calibration baseline → ok / warn / fix + finger-specific tip.
    - analyseStrum() / judgeStrum(): whole-chord check, which strings are missing.
  - Live audio: getUserMedia (echo cancel / noise suppression / auto gain OFF) → highpass 70 Hz → lowpass 5 kHz →
    gain (auto "mic boost") → AudioWorklet tap → 4 s ring buffer. Onsets = level jump over a noise floor.
  - Tuner doubles as calibration: records each open string's ring time, loudness, brightness and buzz baseline
    ("cal" in localStorage). Everything is judged relative to that.
    No calibration yet → askSetup() shows "Quick setup first" (Start setup / Skip for now). Skip saves standard
    settings (cal.est = true, raw 0 = no loudness gate) until a real tuner run replaces them.
  - Flows: checkChord (string by string, stays on a string until fixed, then strum), strumLoop (strum practice).
  - Chord changes: loop of 2–4 chords, times each change, quick 0.5 s strum judge, BPM up after 3 clean on-time
    changes and down after 2 fails, beat dots (no click sound).
  - Habits: every result logged on the phone; shows most repeated mistakes + slowest change; "practice weak spot".
- localStorage keys: fretcoach-cal, -log, -bpm, -seq, -boost, -autoboost, -muteat, -mutever, -screen, -onboarded, -firstchord.

## Tuning notes (learned from real recordings, iPhone mic)
- Muted = rings much shorter than calibration (default under 50%, Setup slider). Barre chords get extra slack.
- Buzz: fretted strings only, and only when there's real energy at 2–5 kHz (near-silent bands gave false buzz).
- Strum: a string is "missing" only if its unshared harmonics are weak. In barre chords (F, Bm) octave-doubled notes
  are judged only against "strong" notes.
- Tried and rejected (worse on real recordings): SwiftF0, Spotify Basic Pitch, spectral-flux onset detection.
- Re-test any detection change against recordings before shipping. Braedon can share the test recordings
  (clean/muted/buzzing C, strums, F and Bm plucks/strums).

## Status
- All 8 basic chords work live. Chord change trainer works well.
- v31: 29 chords, grouped picker, barre diagrams + tips, 2–4 chord change loops. v32: barre chords less strict.
- v37: only the 8 basic chords are shown for now. v38: "Quick setup first" prompt + code-review bug fixes.
- Not yet tested live: 7ths, barre and power chords (hidden for now); "Skip for now" standard settings.

## Working style
- Batch changes; build and upload a new version only when ready. Bump the version tag every release.
- Braedon is learning to code: explain changes with the real technical terms plus a quick plain meaning.
