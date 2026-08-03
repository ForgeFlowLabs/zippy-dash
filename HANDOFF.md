# Zippy Dash — Project Handoff

A browser-based endless dodge game built for submission to CrazyGames, developed by **Flowgames**.

## Files in this bundle

| File | Purpose |
|---|---|
| `index.html` | The full game — single HTML file with inline CSS/JS (named `index.html` so it serves at the site root and satisfies CrazyGames' required entry-point filename) |
| `bg-music.mp3` | Background music track (128kbps, ~97s loop) — **must sit in the same folder** as the HTML file; referenced via relative path (`<source src="bg-music.mp3">`) |
| `cover-landscape.png` | 1920x1080 CrazyGames cover art |
| `cover-portrait.png` | 800x1200 CrazyGames cover art |
| `cover-square.png` | 800x800 CrazyGames cover art |
| `crazygames-docs/` | Full CrazyGames developer documentation (transcribed), used to verify compliance |

**To run locally:** put `index.html` and `bg-music.mp3` in the same folder, open the HTML file in a browser.

## CrazyGames compliance status (audited against `crazygames-docs/`)

Cross-checked against the actual requirement text in `crazygames-docs/`, not just this summary.

**Launch phase — SDK is intentionally OFF for now:**
The CrazyGames SDK `<script>` tag is **commented out** in `index.html` for the
testing / **Basic Launch** phase. Per the docs the SDK is *optional* for Basic
Launch (monetization is disabled then anyway), and loading it off-platform only
throws benign "not initialized" / "ads disabled on this domain" errors. All SDK
integration code stays in place and guarded — re-add the one script tag in the
`<head>` for **Full Launch** (hosted on CrazyGames) and it activates. With the
SDK off, best score persists via `localStorage`.

**Met in code:**
- ✅ Entry point is `index.html`; single self-contained file; all asset paths relative. Well under file-size/count limits. Runs with **no external requests** in the current (SDK-off) build.
- ✅ Full SDK integration written and guarded (dormant until the SDK tag is re-added): `init()`, `loadingStart/Stop`, `gameplayStart/Stop`, Data module (best score), all routed through a `cgCall()` wrapper so a call racing ahead of `init()` can never surface an error.
- ✅ Frame-rate-independent physics (delta-time); mouse + keyboard + touch; AZERTY-safe (`event.code`); no custom fullscreen button.
- ✅ Portrait layout — allowed, with background on the sides (per technical-requirements). Body stacks vertically so the footer sits below the game.
- ✅ Ads only via the SDK; midgame ad requested after death; audio muted on `adStarted`, restored on finish. **Graceful when ads are disabled/unfilled/silent** — a 4s fallback always re-enables "Play Again" so there is no dead button / between-levels freeze (a documented rejection reason for Basic Launch).
- ✅ CrazyGames `game.settings.muteAudio` **overrides** the in-game audio toggles, with a settings-change listener (per SDK docs).
- ✅ Guests can always play; no external login; no personal data collected (no consent notice needed). PEGI-12-appropriate; English.
- ✅ Leaderboard code matches their AES-GCM spec, gated behind a placeholder key (safe no-op until a real key is added — invite-only feature).

**Still required at submission (portal / assets — NOT code):**
- ⏳ **Preview video** — required. 15–20s, no sound, landscape 1080p (16:9) AND portrait 1080p (2:3), ≤50MB, static cover as opening frame, no black bars/cursor/promo text. Not yet produced (needs a screen recording).
- ⏳ **Metadata:** game description + controls text in the Developer Portal.
- ⏳ **Progress Save option** must be selected during submission, or the Data module is disabled regardless of code.
- ⏳ Verify the **"Zippy Dash"** name doesn't collide with an existing CrazyGames title, and confirm cover images have no borders/extra text (title only).
- ⏳ Real **leaderboard encryption key** if/when invited to that feature.

**Optional / nice-to-have (not blocking):**
- `game.reportGameCompletedPercentage()` for an endless game (would need a self-defined progress notion, e.g. vs best score).
- Readability pass in the Portal QA tool at the smallest listed iframe sizes (e.g. 800x450) — the fixed-size overlay/logo can get cramped on very small frames.

## ✅ The "game breaks / won't play" issue — RESOLVED

The long-standing report of the game "running here but now it doesn't" was a **silent NaN corruption in the render loop**, now fixed.

**Root cause:** `startGame()` and `togglePause()` start the loop by calling `loop()` directly (no argument), so on that first frame `timestamp` was `undefined`. That made `elapsedMs`/`dt` `NaN`, and because `player.x` and `player.bob` are delta-time-scaled (`+= … * dt`), they were corrupted to `NaN` **permanently** on frame one. The character then rendered at `ctx.translate(NaN, NaN)` — drawing nothing — so the player was invisible and the game looked dead. Crucially **no error was ever thrown**, which is exactly why the earlier jsdom error-checking passed and no console error could ever be produced.

**Fix:** `loop()` now ignores a call with no timestamp and simply schedules the next real `requestAnimationFrame` (which always supplies one). Verified in a real headless Chromium across all 5 characters — player stays finite, death → game-over → "Play Again" all work, zero errors (aside from the CrazyGames SDK script, which only fails to load in a network-restricted sandbox, not on CrazyGames).

Earlier fixes that remain in place: `safePlayMusic()` wrapper around `bgMusic.play()`, and the manual `pathRoundRect()` helper replacing the newer `ctx.roundRect()` API.

## Deployment / hosting

- The game entry file is **`index.html`** (renamed from `zippy-dash.html`) so it serves at the site root and satisfies CrazyGames' required entry-point filename.
- Hosted on Vercel; a real `index.html` at the repo root serves at `/` natively (no `vercel.json` rewrite needed). If the root ever 404s, confirm Vercel is deploying the latest `main` commit.

## Project history / key decisions

- **Started as:** a generic dodge/arcade prototype, then themed as "Minion Dash" — **renamed to "Zippy Dash"** after flagging IP/trademark risk (both the Despicable Me name association and copying their visual character design).
- **Character roster (final):** originally humanoid "goggle" creatures (still Minion-adjacent in silhouette) — **redesigned as original cute animals** per user request:
  - **Zippy** — fox (forest theme, rock obstacles, acorn collectibles)
  - **Blaze** — bunny (carrot field theme, hay-bale obstacles, carrot collectibles)
  - **Pip** — panda (bamboo forest theme, bamboo-log obstacles, bamboo-leaf collectibles)
  - **Nova** — cat (moonlit/yarn theme, yarn-ball obstacles, fish collectibles)
  - **Coral** — duck (pond theme, mossy-rock obstacles, bread-crumb collectibles)
- Each character has a fully distinct background scene, obstacle type, and collectible type, all drawn procedurally in Canvas (no external image assets).

## Feature checklist (implemented)

- [x] Core dodge gameplay (4-lane endless runner, tap/keyboard controls)
- [x] 5 selectable animal characters with distinct themed worlds
- [x] Combo/streak scoring system (multiplier grows every 10 dodges, capped at x5, with popup + sound on level-up)
- [x] Collectible items per character (bonus points, no penalty for missing)
- [x] Dramatic death sequence: screen shake, red flash, particle explosion, Mario-style pop/spin/fall animation, ~2 second beat before game-over screen
- [x] Background music (embedded via relative-path MP3) + sound effects, with mute toggles for both, separate from each other
- [x] Pause/resume (pauses actual game loop, not just audio)
- [x] Responsive canvas scaling (fits any viewport/iframe size while preserving aspect ratio) + high-DPI backing buffer
- [x] Frame-rate independence (delta-time based physics — verified consistent across 30/60/144Hz equivalent timings)
- [x] Full CrazyGames SDK integration:
  - `init()`, `loadingStart()`/`loadingStop()`, `gameplayStart()`/`gameplayStop()`
  - Midgame ad requests on game-over with proper mute/pause/adError handling
  - Data module for cross-device best-score persistence
  - Leaderboard score submission (AES-GCM encryption per their spec) — **gated behind a placeholder encryption key** (`LEADERBOARD_ENCRYPTION_KEY` constant near the top of the script) since the leaderboard feature is invite-only; currently a safe no-op until a real key is added
  - AZERTY-safe keyboard controls (`event.code` instead of `event.key`)
  - `user-select: none` for mobile, iOS audio-resume-on-touch fix
- [x] Cover images at all 3 required CrazyGames dimensions

## Not yet done

- [ ] **Preview video** (15-20s gameplay clip, no sound, static cover as opening frame) — needs actual screen recording; not something that could be generated directly. Could build a scripted auto-demo mode to make recording easier.
- [ ] Final submission description/controls text — a playful draft exists in conversation history but wasn't finalized in-file
- [ ] Verify "Zippy Dash" doesn't collide with an existing CrazyGames title (needs manual/web check)
- [ ] Actual CrazyGames Developer Portal submission (Basic Launch first, per their recommended flow)
- [ ] Real leaderboard encryption key once/if invited to that feature

## Technical notes for whoever picks this up

- Everything is in **one script block** at the bottom of `index.html` — no build step, no bundler, plain JS + Canvas 2D.
- Game logic operates in a **fixed logical coordinate space** (`W=420, H=640`), decoupled from actual on-screen pixel size via `fitCanvasToViewport()` and a DPR-aware backing buffer. Any new drawing code should use `W`/`H`-relative coordinates, not `canvas.width`/`canvas.height` directly.
- All per-frame motion/timers are **delta-time scaled** (a `dt` parameter flows through `update()`) — do not add new `+= constant` per-frame logic without multiplying by `dt`, or it'll reintroduce frame-rate dependence.
- `CG` is the CrazyGames SDK reference, `null` when running outside their platform — every SDK call is guarded with `if (CG && CG.xxx)` so the game runs standalone for local testing.
- Avoid newer/uncommon Canvas or JS APIs without a compatibility check — this bit us once already with `ctx.roundRect()`.
