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

**To run locally:** put `index.html` and `bg-music.mp3` in the same folder, open the HTML file in a browser.

## ⚠️ Current known issue — UNRESOLVED

The user has reported the game breaking / not playing in their actual browser, on at least two occasions after fixes were applied here. Two real bugs were found and fixed via automated headless-browser testing (jsdom):

1. `bgMusic.play().catch()` could throw if `.play()` didn't return a real Promise in some environments — fixed with a `safePlayMusic()` wrapper (search for it in the file).
2. `ctx.roundRect()` (used in hay-bale/bamboo-log obstacle rendering) is a newer Canvas API not supported in all browsers — throwing there would silently kill the whole `requestAnimationFrame` loop. Replaced with a manual `pathRoundRect()` helper.

Despite both fixes, the user reported "it was running here but now it doesn't" most recently. Automated testing (simulating full playthroughs across all 5 characters, 6+ seconds each, with active input) shows **zero errors** — so the remaining issue may be:
- Environment-specific (a particular browser/device/WebView)
- Related to testing via a sandboxed preview iframe rather than a real browser tab
- Something not exercised by the automated tests (e.g., a specific interaction sequence, timing race, or the CrazyGames SDK script tag failing to load and something downstream assuming it succeeded)

**Next step:** get an actual browser console error from the user (screenshot or copy-pasted text) to pinpoint it — that was the last ask before this handoff.

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
