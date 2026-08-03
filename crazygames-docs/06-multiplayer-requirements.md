# Multiplayer Requirements

Multiplayer games see 2x higher long-term retention than single-player games — CrazyGames strongly supports them via "Play with Friends" (friend requests, join/invite notifications when in a joinable location, powered by the Game module SDK).

Games supporting Friends functionality get featured on the dedicated Multiplayer landing page.

## Requirements for "Online with Friends" (Full Implementation)
- **Share room & status:** report the user's room via SDK so CrazyGames can offer Join/Invite in their UI. Avoid onboarding scenes for joining players, or make them skippable.
- **Update Room:** use `updateRoom` (roomId unique per game/region, `isJoinable`, `inviteParams`) to indicate room state. (Older "Invite Button" method still supported but being deprecated.)
- **Invite Link** (optional): lets sharing a direct join link.
- **Instant Multiplayer:** first player in a party goes straight into a new private room with default settings.
  - `isInstantMultiplayer` flag = true → launch directly into multiplayer mode when triggered from CrazyGames UI (e.g. Multiplayer landing page).
  - An intermediate config screen (mode/player count) is fine.
  - Games supporting 20+ players may place the player directly into public gameplay instead.
  - In all cases, the player must be joinable immediately after launch.
- **Round-based games:** after a match, let players continue with the same group without navigating back through CrazyGames UI (immediate next match in the same room, or all directed to a new shared room).

## Additional Requirements
- Submit lobby size at build upload (contact them to change later).
- CrazyGames usernames must be shown in-game so players recognize friends.
- Chat implementation has its own requirement (see below).

## Guidelines (recommended, not mandatory)
- Let players join an existing room at all times where possible; use spectator mode during an ongoing round, or show a "room not available" popup.
- Implement the Room Join Listener (users often pre-load the game before joining/inviting) for smoother UX without a page reload.
- Use the User module's friends-list method for join/notify/matchmaking UX improvements.

## Multiplayer Games in Basic Launch
- Games needing a large audience for a good experience may skip Basic Launch and go straight to Full Launch requirements.
- Games with a single-player component that don't need a large testing audience should still do a Basic Launch step.
- CrazyGames' QA team decides which flow applies, with optional feedback.

## Chat and User-Generated Content (UGC)
Chat is powerful for engagement but risky:
- Must be able to disable chat via game settings; if complaints arise, CrazyGames may require full disablement.
- Chat moderation is REQUIRED — minimum: a profanity filter (they provide a non-comprehensive blocklist sheet). More advanced: AI moderation via their partner, Lasso Moderation (referral bonus available; not recommended for Basic Launch).
- Same moderation requirement applies to any UGC (custom images/drawings) — Lasso also covers this.
