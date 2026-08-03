# Technical Requirements

## File Size & Count Limits
- **Basic:** Max total file size 250MB. Max 1500 files (high counts slow loading).
- **Basic:** Initial download size ≤50MB. For mobile-homepage eligibility, ≤20MB.
- If SDK integrated: initial download size = time between load start and first **Gameplay start** event via Game module (should fire when player enters a playable state — excludes menus/loading).
- If SDK NOT integrated: total file size is used instead (≤50MB, ≤20MB for mobile homepage).
- Externally hosted/loaded files: QA evaluates based on time-to-gameplay (≤20 seconds).
- **Use only relative paths** — absolute paths will fail to load.

## Device & Browser Compatibility (Basic)
- Must work on Chrome and Edge. Poor Safari support → disabled on Safari.
- Significant Chromebook audience — disabled on ChromeOS if not smooth on 4GB RAM devices.
- Must support mouse, keyboard, and touch (if mobile supported).
- Should be playable in landscape on desktop. Portrait/vertical games allowed (especially if mobile-friendly), with black bars/background images on the sides if needed.
- CrazyGames has device/OS/browser/app-type detection — recommended to use their system info for device-specific experience.

## Mobile Game Requirements
- ≤20MB initial download for mobile-homepage eligibility.
- Configure supported orientation at submission; the site handles orientation-lock prompts — no need to implement your own lock logic.
- Add this CSS to prevent unwanted magnifier/selection popups on long-press/double-tap:
  ```css
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  ```
- Unity games disabled on iOS by default (memory-related crashes) until enough plays for evaluation.
- Mobile games must work inside the CrazyGames App (fullscreen, device safe areas).
- CrazyGames manages Unity DPR: 1x for iOS/low-memory Android, native DPR elsewhere (can be manually overridden).

## Resuming Audio After iOS Interrupts It
**Problem:** Android keeps AudioContext "running" (silently) when backgrounded. iOS suspends the AudioContext on backgrounding/interruption (e.g. phone calls) and needs proactive restoration.

**Solution:** Call `resume()` on the AudioContext within a user-gesture event (e.g. `touchend`), not just on visibility change (WebKit restricts playback until direct interaction):
```js
document.addEventListener("touchend", () => {
    if (audioContext && audioContext.state === "suspended") {
        audioContext.resume();
    }
});
```

## SDK Integration
**Basic SDK Integration (optional for Basic Launch):**
- Must trigger a Gameplay start event from the Game module when the player reaches game state (used to measure initial download size).
- Ads are NOT allowed in Basic Launch even if integrated — will be disabled.

**Full SDK Integration** (includes all Basic requirements plus):
- Gameplay start/stop events (for experience measurement/reporting)
- (if applicable) Data module for progression save
- (if applicable) User module for account integration/username/avatar
- (optional) Load start/stop events for loading-time/fail-rate measurement

## Sitelock & Whitelisting (Basic)
Optional sitelock to prevent game file theft — if used, must whitelist all CrazyGames domains (since they operate on multiple domains).

## User Consent
If the game collects personal data beyond the SDK's own events, must show a Terms & Conditions / Privacy Policy notice to new players.
- Recommend a simple non-blocking notice rather than a popup.
- Examples: Bloxd.io (in-game notice), Racing Limits (opens policy in new tab).
