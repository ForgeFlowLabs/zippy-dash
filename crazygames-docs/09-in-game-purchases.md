# In-Game Purchases (Full Implementation, Invite-Only)

CrazyGames partners with **Xsolla** for in-game purchases. This feature requires an invite from CrazyGames — contact them if interested.

- Purchases must be limited to **signed-in users only** — guests cannot purchase.
- Requires either the User module (if you have a backend) or the Data module (to save progress securely).
- If using Xsolla/another external payment flow, disable it inside the CrazyGames App when `applicationType` is `google_play_store` or `apple_store`.

## Getting Started
Once invited to the Xsolla project dashboard, two auth paths:
1. **Standard, linked to CrazyGames accounts** — get credentials from CrazyGames, use `getXsollaUserToken()` from the SDK; purchases auto-link to the CrazyGames account.
2. **Custom, linked to your own in-game accounts** — generate Xsolla credentials yourself; must reference the CrazyGames userId in every order (either as the main user identifier, or via `crazyGamesUserId` in `custom_parameters`).

```js
const token = await window.CrazyGames.SDK.user.getXsollaUserToken();
```
Retrieve fresh each time before use (short-lived, ~1hr; SDK handles refresh).

## Registering Orders
Typically via Xsolla's Shop-Builder API. Must include the CrazyGames userId per the getting-started note above.

## Testing
`getXsollaUserToken()` only works on crazygames.com itself — test via the Developer Portal preview. Use Xsolla sandbox orders (fake money) during development; **disable sandbox mode before actual submission**.

## Order Tracking (optional)
Track completed (`done`) orders via analytics:
```js
window.CrazyGames.SDK.analytics.trackOrder("xsolla", order); // order = JSON object
```
Also encouraged to track `new` and `canceled` orders for future usefulness.

## Requirements & Guidelines
- Always register purchases against the CrazyGames account ID.
- Must have a working 'close' button on the PayStation widget so players can resume their session.
- If PayStation opens in a new tab, notify players (text only) to allow browser popups.
- Hide any "Back to the game" link after successful payment (set 'Manual redirect condition' to None in Xsolla PayStation settings) — avoids confusion.
- Must correctly handle payment statuses to avoid charging without crediting (e.g. player closes window mid-payment). Options:
  - Webhooks (Xsolla calls your API on events like `order_paid`; use their Webhooks API if you lack your own backend)
  - Inventory endpoint (safest way to retrieve confirmed purchases)
  - Client-side order tracking (useful for instant crediting, but must still be validated via Webhooks/Inventory; avoid navigating away from the shop screen before attribution completes)
- If purchases exist on web AND the game is mobile-friendly, hide/disable those purchase UI elements when running inside the CrazyGames App (unsupported flow there).

## Common Mistakes
- Overcomplicating the integration — check Xsolla's embeddable widget for a simpler setup.
- Mobile back button sometimes hidden — see their linked resource on this.
- Players can accidentally close the Xsolla window via outside-click or Esc — disable via `closeByClick: false` and `closeByKeyboard: false`.
- Unity-specific: in `Assets/Xsolla/Core/Plugins/paystation.jslib`, add those same two options.

## Loot Boxes / Similar Mechanics
No special legal definition used internally — any mechanic granting random item(s) in a sealed "box," openable free or via currency, counts. Main restriction concerns: items bought with real money, or with virtual currency that itself can be bought with real money. "Other loot box mechanics" (wheel of fortune, card packs, etc. with hidden/random content + monetization) are considered case-by-case.

**Sales restrictions apply in specific territories if loot boxes are present:**
- Belgium, China, Netherlands, Serbia, Slovakia — restricted generally.
- Taiwan, South Korea — restricted additionally if odds for same-value items aren't disclosed as percentages.
- Japan — restricted additionally if (a) an obtained item is worth less than the price paid, or (b) the ToS doesn't prohibit real-money trading/secondary-market trading between players.
