# CrazyGames Documentation — Source Material

These are the actual CrazyGames developer docs shared during development of Zippy Dash, transcribed in full (not summarized) so Claude Code — or anyone else picking this up — can independently verify compliance rather than relying solely on the `HANDOFF.md` summary.

| File | Covers |
|---|---|
| `01-quality-guidelines.md` | Onboarding, general principles, aesthetics, restricted keys (non-mandatory but recommended) |
| `02-requirements-overview.md` | Basic vs Full Launch requirement summary table, monetization, analytics |
| `03-technical-requirements.md` | File size/count limits, browser/device compatibility, mobile requirements, iOS audio quirk, SDK integration basics, user consent |
| `04-advertisement-requirements.md` | Video ads, rewarded ads, banners, adblock handling — full policy |
| `05-account-integration.md` | CrazyGames account system, progress save, guest/login flows |
| `06-multiplayer-requirements.md` | Play-with-friends features, chat/UGC moderation (not applicable to this game, kept for reference) |
| `07-game-covers.md` | Cover image specs/restrictions, preview video specs |
| `08-sdk-modules-reference.md` | Full API reference: `ad`, `banner`, `game`, `user`, `data`, leaderboards (client + server) |
| `09-in-game-purchases.md` | Xsolla integration (invite-only, not currently used) |
| `10-gameplay-requirements-and-faq.md` | Core gameplay requirements + selected FAQ highlights |

**Cross-reference:** see `../HANDOFF.md` for how these requirements map to what's actually implemented in `index.html`, and what (if anything) is still a gap.
