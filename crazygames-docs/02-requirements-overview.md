# Requirements Overview

To be published on CrazyGames, a game must meet their requirements, designed to ensure games are fun, unique, visually appealing, and properly integrated.

Launch process = 2 steps:
1. **Basic Launch** — go live without customizing for CrazyGames. SDK optional, monetization NOT available. Used to evaluate progression/real player data.
2. **Full Launch** — once selected, must comply with ALL integration requirements below, including the full SDK.

## Summary table (Basic vs Full Implementation)

**Technical**
- Basic: Initial download ≤50MB; total file size ≤250MB (≤50MB without SDK); file count ≤1500
- Full: SDK Gameplay Start event required

**Gameplay**
- Basic: basic visual QA checks; PEGI12 adherence
- Full: full visual QA check; must land directly in gameplay

**Advertisement**
- Basic: CrazyGames monetization disabled, no external ads
- Full: Ads through SDK following their guidelines; must work with AdBlock

**Account integration** (only when applicable)
- Basic: no external login options
- Full: progress linked to CrazyGames Account; use CrazyGames username/avatar; automatic login for CrazyGames users

**Multiplayer** (only when applicable) — full implementation optional in basic launch but improves engagement:
- User room info, invite link (if applicable), instant multiplayer flow, keep rooms across rounds, DisableChat preference

**In-game Purchases** — invite only, not available in Basic; Full requires using CrazyGames Xsolla account + userId

Their HTML5 and Unity SDKs support all scenarios; other SDKs may miss functionality.

Submission also requires: qualitative metadata (description, controls) and game covers (images + videos).

## Monetization
Primary mechanism = ad revenue share, ONLY through their SDK. Selected games may be eligible for In-game Purchases (Full Implementation + Xsolla, invite-based).

## Insights & Analytics
Developer Dashboard provides: Players, Average playtime, Gameplay conversion, Retention, Revenue. For deeper analytics (level progression, drop-off, user journey) they recommend ByteBrew (free, easy to integrate).

**Warning:** If the game collects personal data beyond SDK events, must add a Terms & Conditions / Privacy Policy notice to new players (see User Consent).

## Technical support
Once a game reaches 50k combined plays, CrazyGames offers technical SDK integration support/feedback on ad placement.

## QA Tool
Developer Portal preview lets you: run the game as it would appear live, check requirement compliance, test SDK features and get feedback.
