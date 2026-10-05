# MAGIC ALGO — Internal Development Log

Purpose: authoritative engineering history for the launcher. Update this file with every version change.

## Architecture rules — PROTECTED
- Prefer references to existing/native functions, state, controls and data. Do not hard-code values that can be derived.
- One feature has one authoritative implementation. All pages/modules consume it by reference.
- Replay is application-level state, never page-specific.
- Replay state includes mode, active timeline/index, active candle timestamp and cutoff.
- LIVE means latest available candle/NOW and must reposition the native replay state to the latest bar.
- Exactly one active-mode indicator: LIVE blinks in LIVE; REPLAY blinks in REPLAY.
- Timestamp is derived from the active candle/data feed. Never hard-code a date/time.
- Scanner and every current/future view consume the same replay cutoff/timestamp.
- Adding future pages must not require changes to replay/timestamp logic.
- Do not add a second/bottom replay scroller.
- Do not simulate replay with repeated Prev/Next clicks, wheel/pan events, MutationObserver or polling hacks.
- Preserve working broker/datafeed/startup behavior unless the version explicitly targets it.
- Prefer the smallest referenced change and validate before expanding scope.

## Version history

### V216 — PROTECTED REFERENCE
- Compact self-contained launcher and robust GitHub auto-update bootstrap.
- Use its embedded native app.js implementation as the primary replay/UI reference.
- Known compact baseline: approximately 125 KB launcher.
- Historical reference commit: 9b2b490039973b2f3ea4feab6537fe70a18108d7.

### V246 — STABLE BROKER REFERENCE
- Known-good broker/UI baseline.
- Launcher commit: d197c9ca9dca52363bcea663f4a19b08c1475015.
- Keep as a broker-behavior reference; do not use its larger launcher as the compact baseline.

### V247–V255 — DO NOT USE AS REPLAY BASELINE
- Experiments included bottom replay bars, synthetic navigation, DOM/polling approaches or regressions.
- Retain only for historical comparison; do not copy those replay mechanisms.

### V256 — ROLLBACK ATTEMPT
- Restored older pre-replay lineage but remained larger than desired compact baseline.
- Not the preferred development base.

### V257 — TESTED / PARTIAL
- Built from V216 lineage.
- START renamed REPLAY.
- Removed unwanted bottom replay scroller.
- Native replay state changed toward latest-bar behavior.
- Screenshot/video confirmed stable rendering and replay progression.
- Remaining issue: visible top blue range control appeared to be speed/control rather than authoritative replay position.
- Launcher commit: 618f3b0c4dcf61e43f15e63437aff2292714bb28.

### V258 — TEST / NOT PROTECTED
- Commit: 96d787bb0ecb980760be1da4caf66c22955315c8.
- Intended: active LIVE/REPLAY blinking, LIVE latest-candle reposition, universal active-candle timestamp.
- Screenshot validation: timestamp did NOT render in the top bar.
- Therefore V258 timestamp implementation is not accepted as the architectural solution.
- Next correction must locate/use the actual shared topbar/native replay references rather than guessed HTML anchors.
- User requirement reinforced: no page-specific replay/timestamp hard-coding; future pages must inherit shared state automatically.

## Validation policy
Each new entry should record:
1. Base/reference version.
2. Exact functions/components changed.
3. Architectural reason.
4. What was deliberately left untouched.
5. Commit SHA.
6. Screenshot/video test status.
7. Known issues.
8. Status: TEST, STABLE, PROTECTED, ROLLBACK, or REJECTED.
