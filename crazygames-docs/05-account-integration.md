# Account Integration Requirements

CrazyGames' account system lets 35M+ users save progress across devices, customize username/avatar, and play with friends. Requirements/expectations:
- No standalone in-game username/avatar needed if using their system.
- No additional in-game login flows needed.
- Guests must always be able to play.

## Four integration scenarios
1. **Games without accounts (Basic)** — no user module integration needed; can use the Data module or APS system to save progress.
2. **Use CrazyGames profile**
3. **In-game account (basic)**
4. **In-game account (full)**

## Progress Save (Full Implementation)
Players care a lot about progress syncing across devices. Required unless progress isn't applicable to the game. Methods:
- **Preferred: CrazyGames Data module** — saves to the user's account. Guest progress is auto-saved locally; syncs to their cloud on login (if no existing cloud progress).
- If the game has its own backend, use the User module to link backend data to the CrazyGames account.
- Handle multi-device/multi-account-per-device scenarios (see In-game account integration flows).
- **Alternative:** Automatic Progress Save system (auto-syncs local progress to cloud) — NOT allowed for games with in-game purchases (relies on local data).

## In-Game Account Integration (Full Implementation)
Requires using the CrazyGames userId as an identifier via the User module.

**At game launch:**
- Call `getUserToken()` for a JWT, verify server-side, extract userId.
- Re-check the current user every launch (covers shared-device / changed-profile scenarios).

**Option 1 — user not logged in (`userNotAuthenticated`):**
- Always allow playing as Guest as the main scenario.
- Don't create an in-game account for guests (unless you can link it to a CrazyGames account on login) — don't rely solely on local data to identify guests across sessions (shared devices).
- A "Login with CrazyGames" button is allowed but must NOT be the main CTA.
- Never auto-trigger the Auth prompt (confusing to users).

**Option 2 — user logged in (userId returned):**
- Check if that CrazyGames userId already exists in your backend.
  - **Known:** update stored username/avatar (they can change), fetch data, play.
  - **Unknown:** auto-create a game account linked via userId (not username, which can change). Optionally migrate local guest progress.

**During the game:**
- Detect guest→login transitions via an Auth Listener, then follow the "logged in" flow / refresh if needed. (Only applies to in-game guest users.)
- On logout, the whole page refreshes automatically — nothing else to handle.

**Login Button:**
- Not the main CTA. Good placement: top-right corner. Must trigger the SDK's Auth prompt method. Don't allow alternate login methods (Facebook/Google/email) for guests — only "Login with CrazyGames".

**Logout & account linking:**
- Logging out in-game + allowing external login methods is NOT allowed.
- If offering import/export of accounts, you're responsible for correct progress migration.
- Optional Account Link Prompt SDK method — strongly recommended if you create in-game accounts for guests; recommended too if using in-game purchases with guest-created accounts.
