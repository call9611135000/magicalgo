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


## Modular controller architecture — PROTECTED
- Each feature is an independently controlled module with a stable identity and explicit controller.
- Initial controllers include REC, Bar Replay, Timestamp, Scanner, Datafeed, and Broker/Execution; future controllers follow the same contract.
- A controller owns only its module state, UI exposure, config, status/errors, lifecycle, runtime/process ownership, cache/log/data paths, and declared dependencies.
- Controllers communicate through stable shared references/events; they must not depend on page names or duplicate another module's state.
- Shared application state stays minimal. Example: Bar Replay publishes active mode/index/candle/cutoff; Timestamp and Scanner consume those references.
- Every module must be independently installable, enabled/disabled, testable, debuggable, updateable, repairable, removable, reusable and packageable.
- Removing a module must remove/disable only resources it owns and leave unrelated modules untouched.
- No dangling hard-coded references are permitted after module removal.
- Project assembly is registry/dependency driven: e.g. FYERS only, Scanner only, FYERS + Scanner + Replay, or any future valid combination.
- One-click ADD / REMOVE / BUILD / UPDATE operations are architectural targets.
- The main launcher/core is an orchestrator/project builder, not the owner of module-specific behavior.
- New pages consume controller interfaces; adding pages must not require modifying stable controllers.


### V259 — CONTROLLER FOUNDATION / TEST
- Base: V258.
- Commit: f9c551094e1e1fe6e67d597d7c8cd97890bac88d.
- Added explicit reusable controller registry for Timestamp, REC, Bar Replay, Scanner, Broker, Datafeed and Execution.
- Dependencies are declared rather than inferred from page names: Timestamp -> Bar Replay; Scanner -> Bar Replay; Execution -> Broker.
- This is the controller foundation only; feature behavior remains referenced from the existing implementation while controllers are separated incrementally.
- No page-specific controller copies added.
- Status: TEST until screenshot/runtime validation.


### V260 — HARDCODE CONTROLLER / PROTECTED
- Base: V259.
- Commit: 9187b3b6315d336f571631a6a628c9af08ff7f30.
- Added Hardcode Controller to the reusable controller registry.
- Added one centralized HARDCODE_REGISTRY; it is intentionally empty at introduction.
- Protected rule: if a native/shared/reference-driven value exists, hardcoding is prohibited.
- Any genuinely unavoidable fixed value must be declared centrally with ownership/scope/reason rather than scattered through Python, JavaScript, AFL, adapters or individual modules.
- Hardcode Controller is independently inspectable and is intended to support later replacement of fixed values with references.
- Multi-dimensional control remains the target: Controller -> Module -> View -> Environment -> State -> Lifecycle -> Build.
- Status: TEST until runtime/screenshot validation.
