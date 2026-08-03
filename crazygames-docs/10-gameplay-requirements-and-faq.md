# Gameplay Requirements

Not looking for "perfect" games — goal is helping players discover well-crafted games without inappropriate/subpar content. Only quality-and-gameplay-focused games are allowed. Repeated non-compliant submissions may lead to restrictions on future submissions. (Inspired by Facebook Instant Games' best practices.)

## Basic Gameplay Requirements
- **Readable content:** text/images legible at `devicePixelRatio:1`, across their listed responsive iframe sizes:
  - Desktop non-fullscreen: 907x510, 1216x684, 1077x606, 821x462
  - Desktop fullscreen: 1366x768, 1920x1080, 1536x864, 1280x720
  - Mobile: 800x450
  - Tablet: 1080x607
- **Consistent physics:** must behave the same across different monitor refresh rates (144Hz, 165Hz, etc.) — i.e., frame-rate independent.
- **Language support:** English required. If translations are included, must be accurate/high-quality; ideally auto-detect via SDK's `locale` (system info), falling back to English if unavailable.
- **Intuitive controls** across device types (see Restricted Keys in quality-guidelines doc).
- **Smooth performance:** loads quickly, plays without errors/crashes.
- **Originality:** names, assets, and content should be original.
- **Fullscreen handled by CrazyGames automatically** — custom in-game fullscreen buttales are PROHIBITED (can interfere with other features incl. monetization).
- **No cross-promotion** for external/internal games or platforms, with narrow exceptions:
  - Privacy Policy / T&C links are fine.
  - Community links (Discord, dev website) allowed on the game menu ONLY if they don't lead to another playable web game, and aren't the main CTA.
  - Game store (Epic, Steam, etc.) links allowed on desktop games only, in the main menu or at a demo's end.
  - Backlinks to CrazyGames home/category pages accepted but not promoted.
  - Links to other games in the same series (e.g. sequels) are fine.
  - App Store links are NEVER allowed in-game — use the Developer Portal's metadata fields instead.
- **Suited for minors:** CrazyGames' audience is 13+; games must be **PEGI 12** compliant. (A separate kids.crazygames.com domain exists for younger audiences, but monetization is disabled there.)

## Full Gameplay Requirements (Full Implementation)
- Must land new users directly in gameplay.
- If truly not feasible, max 1 click allowed before gameplay starts.

---

# Selected FAQ highlights

**Do I need the SDK?** Optional for Basic Launch (no monetization). Required for Full Launch (unlocks ads, analytics, cloud saves, social features).

**Can I publish an already-live/previously-published game?** Yes, as long as you hold distribution rights — multi-platform strategies are common and encouraged.

**Do I keep ownership of my game?** Yes, 100%, always.

**Why are ads disabled during Basic Launch?** To keep player experience/engagement metrics clean during early testing (no ad-interruption bias) before wider rollout.

**Will CrazyGames iframe my game if hosted elsewhere?**
- No, if hosted by a *competing* browser game portal.
- Yes, if hosted on your own independent domain — but hosting directly on CrazyGames is recommended for full optimization benefits. Revenue only generates if the SDK is correctly integrated, regardless of hosting location.

**Do I need to add the CrazyGames logo?** Not required, but appreciated (assets available from them if desired).

**Can I resubmit a rejected game?** Yes, after making meaningful improvements addressing their feedback.

**How do rankings/homepage placement work?** Based on selection algorithms factoring device/country/OS and some personalization from player history; driven by playtime, retention, conversion, and feedback. New games get an initial visibility boost.

**Monetization eligibility requires:**
- No branding from another game portal
- CrazyGames SDK integrated
- No external ads
- Original, clearly distinguishable from existing games

**Payment:** monthly payouts once balance hits €100 minimum (rolls over otherwise); via wire transfer or PayPal.

**Audience scale:** 50M+ monthly players, concentrated in US/UK/Australia and other Tier-1 markets; broad cross-generational appeal (not primarily a kids' platform).
