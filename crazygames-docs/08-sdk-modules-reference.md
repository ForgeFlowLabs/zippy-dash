# CrazyGames SDK — Module Reference

SDK modules: `ad`, `banner`, `game`, `user`, `data`, and In-game Purchases (not a separate module). HTML5, Unity, and Godot SDKs support all scenarios; other engine SDKs may lack some functionality (usually still achievable via the HTML5 layer).

Access pattern for all modules (HTML5): `window.CrazyGames.SDK.<module>`

---

## `ad` module (video ads)
See advertisement-requirements doc for full policy — this covers the API shape.

```js
const callbacks = {
  adFinished: () => console.log("End midgame ad"),
  adError: (error) => console.log("Error midgame ad", error),
  adStarted: () => console.log("Start midgame ad"),
};
window.CrazyGames.SDK.ad.requestAd("midgame", callbacks);
// or
window.CrazyGames.SDK.ad.requestAd("rewarded", callbacks);
```
Mute audio + pause game on `adStarted`; unmute/resume on `adFinished`/`adError`.

Error codes: `adsDisabledBasicLaunch`, `unfilled`, `adblock`, `adCooldown` (~3 min typical interval accounting for rewarded/preroll too), `other`.

**Adblock detection:**
```js
const result = await window.CrazyGames.SDK.ad.hasAdblock();
```
Game must still function for adblock users — can gate cosmetic/bonus content, never core play.

---

## `banner` module
5 static sizes: Leaderboard (728x90), Medium (300x250), Mobile (320x50), Main (468x60), Large Mobile (320x100).

```html
<div id="banner-container" style="width: 300px; height: 250px"></div>
```
```js
try {
  await window.CrazyGames.SDK.banner.requestBanner({ id: "banner-container", width: 300, height: 250 });
} catch (e) { console.log("Banner request error", e); }
```

Responsive banner (fits container, picks from 11 standard sizes automatically):
```html
<div id="responsive-banner-container" style="width: 500px; height: 500px"></div>
```
```js
await window.CrazyGames.SDK.banner.requestResponsiveBanner("responsive-banner-container");
```

Refresh by calling request again with the same container id. Clear via:
```js
window.CrazyGames.SDK.banner.clearBanner("banner-container");
window.CrazyGames.SDK.banner.clearAllBanners();
```
Limits: 30-second minimum between refreshes per container; max 120 refreshes per session per size; banner must be fully visible/on-page.

Error codes: `bannersDisabledBasicLaunch`, `unfilled`, `missingId`, `notVisible`, `noAvailableSizes`, `notCreated`, `videoAdPlaying`, `invalidSize`, `bannerCooldown`, `maxRefreshReached`, `bannersDisabledMobileApp`, `other`.

---

## `game` module

**Settings:**
```js
window.CrazyGames.SDK.game.settings; // { disableChat, muteAudio }
```
- `muteAudio` MUST override any in-game audio toggle — don't let an in-game "audio on" override this.
- Local testing: `?disableChat=true`, `?muteAudio=true` query params force these.
- Listener: `addSettingsChangeListener(listener)` / `removeSettingsChangeListener(listener)`.

**Gameplay start/stop** (tracks playing time; also used to measure initial load size):
```js
window.CrazyGames.SDK.game.gameplayStart(); // on start/resume/revive/next level
window.CrazyGames.SDK.game.gameplayStop();  // on any break: menu, level end, pause
```
(Don't call stop on tab/focus loss — CrazyGames handles that itself.)

**Loading start/stop:**
```js
window.CrazyGames.SDK.game.loadingStart();
window.CrazyGames.SDK.game.loadingStop();
```

**Happytime** (celebratory confetti effect on the site) — use sparingly, for real milestones only (not every level/item):
```js
window.CrazyGames.SDK.game.happytime();
```

**Game completion percentage:**
```js
window.CrazyGames.SDK.game.reportGameCompletedPercentage(50); // 0-100
```
Report intermediate progress where meaningful; for endless/sandbox games, define your own consistent notion of "100%". Progression should generally move forward; re-report correctly if new content is added (a previously-100% player might now be at a lower %).

**Game context** (attach debug info to user feedback reports):
```js
window.CrazyGames.SDK.game.setGameContext({ level: 12 });
window.CrazyGames.SDK.game.clearGameContext();
```

**Multiplayer-specific (see multiplayer-requirements doc):**
```js
window.CrazyGames.SDK.game.isInstantMultiplayer;
window.CrazyGames.SDK.game.updateRoom({ roomId: "123eu", isJoinable: true, inviteParams: {...} });
window.CrazyGames.SDK.game.leftRoom();
window.CrazyGames.SDK.game.addJoinRoomListener(listener);
window.CrazyGames.SDK.game.removeJoinRoomListener(listener);
window.CrazyGames.SDK.game.inviteLink({...params});
window.CrazyGames.SDK.game.getInviteParam("roomName");
window.CrazyGames.SDK.game.inviteParams; // null if not launched via invite
// Deprecated: showInviteButton(...) / hideInviteButton()
```

---

## `user` module

**Availability check** (not available on domains embedding the game outside CrazyGames):
```js
const available = window.CrazyGames.SDK.user.isUserAccountAvailable;
```

**Current user:**
```js
const user = await window.CrazyGames.SDK.user.getUser(); // null if not logged in
// { __dangerousUserId, username, profilePictureUrl }
```
`__dangerousUserId` must NOT be used for authentication (client-side, spoofable). Use the token instead.

**System info:**
```js
const systemInfo = window.CrazyGames.SDK.user.systemInfo;
// { countryCode, locale, device: {type}, os: {name,version}, browser: {name,version}, applicationType }
```
Use `locale` (not manual detection) if auto-setting game language.

**Friends list:**
```js
const friendsPage = await window.CrazyGames.SDK.user.listFriends({ page: 1, size: 10 }); // max size 50
```
Errors: `userNotAuthenticated`, `rateLimited` (250ms), `requestInProgress`, `unexpectedError`.

**User token** (for server-side auth):
```js
const token = await window.CrazyGames.SDK.user.getUserToken();
```
- 1-hour lifetime, SDK handles refresh — don't store it, re-fetch when needed.
- Verify server-side via the public key at a CrazyGames-hosted URL (re-fetch each time, or cache + refetch on decode failure since the key can rotate).
- **Never decode the token client-side** (insecure).
- Errors: `userNotAuthenticated`, `unexpectedError`.

**Auth prompt** (shows login/register popup):
```js
const user = await window.CrazyGames.SDK.user.showAuthPrompt();
```
Errors: `showAuthPromptInProgress`, `userAlreadySignedIn`, `userCancelled`.

**Auth listener** (fires on login only, not logout — logout triggers a full page refresh):
```js
window.CrazyGames.SDK.user.addAuthListener(listener);
window.CrazyGames.SDK.user.removeAuthListener(listener);
```

**Account link prompt** (standard modal for linking CrazyGames account ↔ in-game account):
```js
const response = await window.CrazyGames.SDK.user.showAccountLinkPrompt(); // { response: "yes" | "no" }
```
Errors: `showAccountLinkPromptInProgress`, `userNotAuthenticated`.

**Local testing overrides** (on localhost/127.0.0.1):
- `?user_account_available=false`
- `?show_auth_prompt_response=user1|user2|user_cancelled`
- `?link_account_response=yes|no|logged_out`
- `?user_response=user1|user2|logged_out`
- `?token_response=user1|user2|expired_token|logged_out`
(Defaults: `getUser`→user1, `getUserToken`→user1's token, `showAccountLinkPrompt`→yes, `showAuthPrompt`→user1, `isUserAccountAvailable`→true.)

**Xsolla (in-game purchases, invite-only):**
```js
const token = await window.CrazyGames.SDK.user.getXsollaUserToken();
```
Purchases auto-link to the CrazyGames user account when using this token.

---

## `data` module (progress save)
Same API shape as `localStorage`. For guests, stored in browser localStorage; syncs to their account automatically on login (or transfers guest data if they've never played before).

**Must init the SDK first** (ideally during your loading screen — it preloads all user data on init):
```js
await window.CrazyGames.SDK.init();
```

```js
window.CrazyGames.SDK.data.setItem("gold", 100);
window.CrazyGames.SDK.data.getItem("gold");
window.CrazyGames.SDK.data.removeItem("gold");
window.CrazyGames.SDK.data.clear();
```
- Best practice: read existing data before writing, to avoid clobbering prior progress.
- 1MB data limit total (console warnings appear when approaching it; data stops being backed up past the limit).
- Saves are debounced ~1 second (occasionally up to 30s).
- **Must select the correct Progress Save option during submission**, or the Data module is disabled regardless of code.
- Migrating an already-published game: copy existing localStorage keys into the Data module so players don't lose progress.

---

## Leaderboards (invite-only feature)

**Config (set in Developer Portal):**
- Leaderboard Guide (≤50 chars, explains how to rank — e.g. "Endless Mode - Survive as long as possible")
- Encryption Key (32-byte base64, for client-side SDK submission) and/or API Key (32-byte base64, for server-side API submission) — at least one required
- Metric Type: `XP` | `KDA` | `POINTS` | `MINUTES`
- Incremental scoring: true/false
- Score sorting: `ASC` (lower=better, e.g. time) or `DESC` (higher=better, e.g. points)
- Min/Max allowed score, cooldown interval (seconds between client-side submissions — not enforced on backend API submissions)

**Client-side submission (SDK):**
```js
export async function encryptScore(score, encryptionKey) {
  const iv = window.crypto.getRandomValues(new Uint8Array(12));
  const algorithm = { name: 'AES-GCM', iv };
  const keyBytes = new Uint8Array(atob(encryptionKey).split('').map(c => c.charCodeAt(0)));
  const cryptoKey = await window.crypto.subtle.importKey('raw', keyBytes, algorithm, false, ['encrypt']);
  const dataBuffer = new TextEncoder().encode(score.toString());
  const encryptedBuffer = await window.crypto.subtle.encrypt(algorithm, cryptoKey, dataBuffer);
  const combined = new Uint8Array(iv.length + encryptedBuffer.byteLength);
  combined.set(iv);
  combined.set(new Uint8Array(encryptedBuffer), iv.length);
  return btoa(String.fromCharCode(...combined));
}

const encryptedScore = await encryptScore(finalScore, encryptionKey);
CrazyGames.SDK.user.submitScore({ encryptedScore, score: finalScore });
```
Note: the server response to a client submission always reports success regardless — actual validation happens server-side, to prevent tampering feedback loops.

**Server-side submission (API)** — for games with a backend, recommended since it can't be bypassed client-side:
```
POST https://leaderboard.crazygames.com/leaderboard/scores
Headers: X-API-Key: <key>, Content-Type: application/json
Body: { "scores": [{ "userId": "...", "score": 123, "timestamp": "ISO8601" }] }
```
- Batch up to 100 scores/request; 1000 requests/60s rate limit (429 + Retry-After header if exceeded).
- Response includes `success`, `total`, `successCount`, `failureCount`, `errors[]`.
- Error types: `no-active-season`, `user-not-found`, `privacy-disabled`, `validation`, `internal-server-error`.
- **Validation errors reject the entire batch** (fix and resubmit everything); non-validation errors (`user-not-found` etc.) allow partial success.
- Best practices: batch submissions, handle partial failures, respect rate limits with backoff, validate scores against configured min/max before sending, use the actual timestamp the score was achieved (not submission time).

**Testing:** Developer Portal preview tool shows `submitScore` calls in logs/console.
