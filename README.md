# Inside Joke — Party Card Game

A mobile-friendly party card game groups play together from their phones while a shared TV or laptop screen (`/tv`) shows the live state. Players sign in, join a persistent "group" (friends or family) via invite code or QR, and build a shared pool of custom prompt/response cards — often photos or short videos of real inside jokes — in a Card Bank. A host starts a session, an optional "Card Rush" lets everyone add last-minute cards, then rounds play out Apples-to-Apples style: a rotating judge poses a prompt card, everyone else submits from their hand, the judge picks a favorite, and the server enforces every rule of play.

**Live:** [insidejoke.vercel.app](https://insidejoke.vercel.app)

**Status:** live and in use. 22 players across 6 groups have played 338 rounds. Works end to end: sign-in, group creation, card building, live multiplayer rounds, TV display and scoring.

**How it was built:** I designed the product and directed an AI coding agent (Claude Code) to write it, approving every change, including 50+ database migrations. Privacy is enforced at the database level, so only a group's members can read its content; I also closed a realtime broadcast leak of joke text.

## Stack

- **Frontend:** React + Vite, deployed as a static SPA
- **Backend:** Supabase (Postgres, Auth, Realtime, Storage)
- **Styling:** Tailwind CSS

## Why this was technically interesting to build

**Server-side game enforcement, not client trust.** Every rule that matters for fairness — dealing hands, validating submissions, picking a winner, deduping repeat cards, resolving timers — runs inside Postgres via `SECURITY DEFINER` functions rather than in client JavaScript. A player's browser can't submit an invalid card, see another player's hand, or force a round outcome by racing requests; the database is the arbiter, not a suggestion. That constraint shaped the schema from day one: game state transitions had to be modeled as atomic server-side operations instead of "read state, mutate in JS, write it back."

**Real-time sync without a custom server.** All connected players and the TV display stay in sync via Supabase's realtime channels (Postgres change feeds pushed over websockets), so a judge's pick or a new round appears instantly on every device without polling or a bespoke WebSocket backend.

**A read-only public display surface.** The `/tv` route is a separate, unauthenticated view designed to be pulled up on a shared screen — it renders the same live game state as the players' phones but exposes no game actions, which meant carefully separating "what this screen may read" from "what this screen may do."

**Progressive identity instead of an upfront login wall.** The app opens an anonymous Supabase session automatically on load so a new player can start interacting immediately, then gates actual gameplay behind an upgrade to a real identity (email or Google) via a dedicated linking flow — trading a harder security boundary for a softer first-run experience, without weakening enforcement once real game actions are on the line.

## Screenshots

None yet — the project doesn't currently have any captured screenshots or a demo recording. This section will be updated when some exist.

---

Full source is private — this repo is a technical overview for portfolio purposes.
