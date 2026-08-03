# Advertisement Requirements (Full Implementation)

**Warning:** During Basic Launch, ads are disabled and no revenue is shared. If Ads SDK is integrated anyway, CrazyGames' QA checks the game runs smoothly with ads disabled (e.g. no freezing between levels, no dead rewarded-ad buttons). Game is rejected if it doesn't handle this.

Only ads requested through the CrazyGames SDK are allowed. Types available:
- Video ads — midgame (between levels/stages) and rewarded (with a reward)
- In-game banners

Ads/purchases must feel meaningful and not appear before a reasonable amount of gameplay. Must NOT:
- Interrupt gameplay
- Trigger deceptively
- Chain multiple ads

## Video Ads
- Cannot interrupt gameplay or come as a surprise — show at logical points (level transition, after death, etc.), never on navigational buttons (menu icon, settings, shop).
- Game must be paused during a video ad — disable buttons or show a blocking spinner while the ad request is in flight (requests aren't instant — auctions take time). Block UI until `adFinished` or `adError`.
- Handle unfilled ad calls (`adError`) correctly — game must continue smoothly regardless.
- Mute game audio only when the ad actually **starts** playing (`adStarted`), not when requested — and unmute on finish. Don't mute/unmute without a visual change if no ad is shown (unfriendly UX).
- Midgame ads can be requested at any opportune moment without worrying about frequency — CrazyGames auto-handles timing (max 1 every 3 minutes, in tandem with rewarded ads). Early requests are simply ignored with no impact.

## Rewarded Ads
Should be special/optional, not a requirement to progress. Poorly designed levels that require a rewarded ad to complete are unacceptable.

**Placement/frequency:**
- Don't offer too often — use a timer or hide the button.
- Don't chain multiple ads for one reward.
- Don't over-promote — a well-implemented reward doesn't need constant reminders.
- The request button must not appear on an active gameplay screen (e.g. not mid-race).

**Reward UI:**
- Button in a consistent, easily accessible location.
- Not misleading — "continue without watching" must match style/size/font of the ad option.
- Must be immediately clear the reward is optional (no hidden/delayed skip button).
- Must be clear a video ad is required for the reward (e.g. video icon).
- Provide an alternative to watching an ad (e.g. paying with in-game coins).

**Callbacks:**
- On `adFinished`, clearly show the reward (animation/notification).
- On `adError`, do NOT reward the player.
- CrazyGames aims for high fill rate and provides alternative incentives when ads aren't available.

## In-Game Banner Ads
- Only allowed on screens open ≥5 seconds on average.
- Must not block any game UI at any game size (incl. mobile).
- Never show during gameplay itself.
- Must be clearly distinguishable from game content.
- Max 2 banners per screen/view, and only if UX stays clear/non-intrusive.
- Can have a performance impact — consider this tradeoff.

## Adblockers (Full Implementation)
CrazyGames disables certain functionality and blocks rewarded ads when an adblocker is detected, but detection isn't 100% reliable, so:
- **Never** block AdBlocker users from playing, or penalize them with disadvantages.
- OK to block special features tied to ads — but must show a notice explaining why (adblock-related).
- Don't use popups (interferes with fullscreen + CrazyGames' own adblock notices).
- Never leave rewarded-ad buttons clickable-but-non-functional.
