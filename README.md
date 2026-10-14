# Pokémon Baseball v4 — Draft-Day Signals & Special Events (15-Pokémon Rosters)

A runnable Node.js fantasy baseball website. The archive keeps the **same `pokebase-main/` directory structure** as the GitHub export supplied for editing. The working implementation is `engine.js`, `server.js`, and `public/{app.js,index.html,style.css,pokemon-data.json}`; original reference JavaScript files remain at the repository root, unchanged.

## Start or publish

1. Install Node.js 22 (Node 20+ also works). In the **same folder as `package.json`**, run `npm start`.
2. Browse to `http://localhost:3000/draft`. Create a fantasy league or choose Solo Challenge.
3. To publish: copy the contents of `pokebase-main/` into the **root** of the GitHub repository already connected to Render. Keep `public/` intact. Commit and push. Render Web Service: start command `npm start`; build command `npm install`.
4. Use the existing Render URL to invite friends; no new hostname is needed. A new 15-Pokémon league is required after this upgrade; earlier 10-slot leagues cannot be completed in the new system.

**Persistence warning:** `server.js` stores leagues in `data/leagues.json`. Render's ephemeral free instances can lose this file on a redeploy/restart. Don't promise persistent multi-day leagues until a managed database is added.

## What's new in v4

- **Roster unchanged from v3:** ten starters (including P and DH) and **five unrestricted bench slots**, for **15 picks per team**. The uploaded `pokebase-main(1).zip` had older ten-slot code; v4 deliberately carries forward the separately supplied v3 15-slot engine instead of reverting it. The outer folder and Render paths remain `pokebase-main/{server.js,engine.js,public/...}`.
- **Clean baseball diamond:** a Pokémon already placed on the field has no opaque background/border or name bar. Its sprite floats over the field with only a small **position label** below it. Empty places remain visible. Bench slot boxes stay below the field.
- **No hover scouting:** hovering, mouse-focusing or moving the pointer over Pokémon doesn't bring up a profile. Deliberately selecting a candidate still shows the six Pokémon base stats, height, weight and colored type badges. Personal fits and the obsolete synthetic baseball attributes remain hidden.
- **Clock and order:** the draft panel glows with a gold call-to-action when your turn starts. It displays nearby picks plus a collapsible **complete snake order** with player names, round and CPU/AUTO indicators. A tiny synthesized two-note sound plays when your turn starts if sound is enabled and the browser permits audio; it can be toggled off in the header. Browsers can block sounds until an interaction.
- **CPU / takeover:** normal CPUs wait **at least 5 seconds per pick**. A human who lets two consecutive timed picks expire is marked **AUTO-DRAFT**; future bot picks also wait five seconds. Manual picks or any real pointer, keyboard or scrolling interaction reset automatic control. Passive polling does not count as interaction. In untimed leagues players cannot miss a deadline, so this automatic takeover doesn't occur.
- **Seven event types:** walk-off home run (targeted 20% when a home-side situation allows a game-ending homer), extra innings (about 15% intentional late rallies, **maximum 15 innings**), pitcher no-hitter (rare, zero hits allowed), grand slam (HR with three occupied bases), comeback win (at least five runs behind earlier), complete-game shutout (pitcher throws at least 27 outs while allowing no runs), and three-home-run game. The simulated score and player box stats generate their special-event metadata. No-hitters, grand slams, etc. are not simply 20% dice labels. Arcade-style forced-extra-innings situations use credited late-inning walks to reconcile run totals.
- **Slower playback:** normal is **0.8 seconds per game**, fast 0.4 seconds, slow 1.5 seconds; pauses are longer for fatigue or special events. Pausing, stepping and skipping remain available. Extra-innings labels show innings played, walk-off winning scores glow briefly, and no-hit pitchers are featured by the result.
- **Championship results:** once playback finishes, the World Series champion and **4–X** series record are featured beside league chat at the top. Underneath are all teams' roster viewers, final standings, individual batting and pitching stats, best-of-seven results, and a browsable log of every regular-season game.
- **Color and personality:** plum/navy, stadium gold, orange, teal and blue-gradient UI accents, scoreboard plaques and updated controls. No generated art or new image assets.

## Preserved systems from v3

- Exactly **15 Pokémon per team**: nine non-pitching starting roles (C, 1B, 2B, 3B, SS, LF, CF, RF, DH), one starting P, **five unrestricted bench slots** (BN1–BN5). The 15 rounds per team retain the original snake draft, 3–6 teams, CPU seats/expired human turn auto-picks, 30/60/120-second or unlimited timer, invite codes, chat, shiny draft rolls, type/generation pool choices, and post-draft lineup swaps.
- Larger baseball diamond. Five bench cards sit **immediately below in one horizontal row** (scrollable/compact on narrow displays). Both starter and bench cards support click-to-swap; on a desktop fantasy draft they also accept drag-and-drop. The original fields remain selectable while drafting. You can place a draft choice into a bench slot.
- Only deliberately selected candidate cards show **exactly six base Pokémon stats, all of its types, height, and weight**. Types have colored badges. The old synthetic POW/CON/FLD/SPD/VEL/CTRL/STA tiles, per-Pokémon fit grids and individual field fit numbers have been removed from the UI. Position fits are intentionally hidden. The **initial team score** is revealed only when all fifteen Pokémon have been selected. The collapsible Scouting Rules section still provides general principles and weight percentages but not anyone's personal fit.
- **HP is excluded from position fit** and controls endurance only. Positional fit uses Attack, Defense, Special Attack, Special Defense, Speed, Height, Weight, plus small balanced type bonuses (+0–3, highest applicable type only). All ten position-specific weights are in `POSITION_WEIGHTS.csv`; the type balance was retained from v2.1 and verified by `type-balance-test.js`.
- All Pokémon start at **100 energy**. Every game each participating Pokémon loses energy based on the original HP stat and role: `round(base × 100 / (HP + 25))` (capped at 4–80). Base costs: **P 43**, C 20, other fielders 17, DH 12. Pitchers tire fastest; high HP lasts longer. Performance is slightly reduced when energy falls below 40; energy does not directly add to fit.
- A Pokémon is **tired at 25 energy or lower after a game** and must miss the **next** game, then fully recovers and returns to its original starting spot. The strongest available rested bench Pokémon **for that position** replaces it for that one game. Unused bench Pokémon recover up to 30 energy per game. Bench substitutions are chosen automatically; they can also become tired after playing. When more than five starters would simultaneously need to rest, only the five lowest-energy starters are scheduled for rest, with additional fatigue kept just above the threshold until a backup slot is available. This avoids an impossible game and is disclosed as a capacity safeguard.
- In both Solo and Fantasy games, the server/engine calculates an entire 162-game season, **then the browser plays the saved results back one at a time**. Default normal playback is ~0.8 seconds per game with a longer pause after tired events; Pause/Resume, Next, Speed and Skip to Results buttons are available. Every game's score, rested players, new tired players and substitutes are shown. The league's live playback displays updated standings. This is **playback of deterministic simulation results, not server-side turn-by-turn simulation**.
- The 162–0 solo challenge retains generation/type rolls, one region and one type reroll, up to three legendaries, and the 151 Challenge with five Pokémon rerolls. Each draft now fills 15 slots. Both season results track batting and pitching **by Pokémon identity**, not just by original position: a substitute's at-bats and pitching innings accrue to that specific Pokémon. Regular-season and best-of-seven fantasy finals use fatigue and bench rotations. Game logs contain the active lineup and fatigue/substitution reports.
- Draft and results layouts use independent flexible sections, avoiding the former tall empty sidebars. Detailed seasonal tables now occupy their own full-width section with horizontal overflow; large fields remain visible.

## Position weighting (fit / 100)

Every row has **0% HP** and percentages totaling 100 across the other seven measures. `SpA` is primarily throwing/pitching technique, `SpD` teamwork, `Atk` power, `Def` fielding, `Spe` movement/baserunning, size factors recovery/reach. We retain logarithmic weight normalization to avoid huge Pokémon dominating.

| Position | Attack | Defense | SpA | SpD | Speed | Height | Weight |
|---|---:|---:|---:|---:|---:|---:|---:|
| C | 10% | 26% | 15% | 20% | 8% | 11% | 10% |
| 1B | 26% | 15% | 10% | 11% | 8% | 15% | 15% |
| 2B | 10% | 27% | 11% | 21% | 21% | 6% | 4% |
| 3B | 23% | 26% | 12% | 15% | 12% | 7% | 5% |
| SS | 9% | 30% | 10% | 20% | 24% | 5% | 2% |
| LF | 21% | 19% | 12% | 15% | 22% | 6% | 5% |
| CF | 10% | 26% | 12% | 17% | 29% | 4% | 2% |
| RF | 25% | 22% | 12% | 14% | 17% | 6% | 4% |
| DH | 38% | 5% | 13% | 10% | 23% | 6% | 5% |
| P | 15% | 7% | 40% | 18% | 4% | 9% | 7% |

Team Score remains a **0–1000** weighted average of ten starter position fits with a weak-position penalty (84% weighted fit + 16% lowest-two average; normalized to 0–1000). Bench depth changes game outcomes, but doesn't add points to the initial team score. No real MLB prediction accuracy is claimed: this is an arcade-style simulator.

## Testing

Execute from the project root:

```bash
node type-balance-test.js
node fatigue-test.js
node smoke-test.js
node draft-bot-test.js
node special-events-test.js
```

These verify type parity, all position weights, mandatory one-game rests and automatic replacements, 15-Pokémon roster sizes, 45-pick three-team snake draft, unique species per league, server chat/lineup swaps, 162 games/team, and results by player identity. The engine and HTTP API integration tests pass. Browser navigation was restricted in the latest editing environment, so full graphical verification of the new v4 layouts and remote sprites is still recommended on your deployed site.

## Notes

- The working backend is a conventional Node HTTP server, not a Vercel Functions application. It can run unchanged on a compatible Node hosting service, with the caveat about local persistence above.
- If you previously played on an earlier build, start a **new solo challenge** to avoid mixing saved ten-slot drafts with the new fifteen-slot roster. You can use the **New challenge** button.
- The supplied original football reference code remains in the ZIP for reference only; the website doesn't load it. Changes to `engine.js` and `public/` control the current game.
- Pokémon sprites load from public PokéAPI sprite hosting; names and artwork remain the rights of their owners. This is an unofficial fan project.
