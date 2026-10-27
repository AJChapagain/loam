# LOAM — Game Design Document (v1 spec)

Source of truth for building LOAM. If a decision changes, update this file
first, then the code.

## 1. Pitch

A daily garden game. Tagline: "Today's plot is ready."
Home line: "Plant your 12 seeds. Whatever blooms stays in your garden."
("Puzzle" is deliberately not used — it sounds like homework. The game
is a garden, not a test.)

Every morning every player gets the **same** plot,
grows it, and keeps a row of it forever. Calm, satisfying, two minutes,
and a garden that visibly grows day after day.

## 2. Core loop

1. Open the game. Today's plot is a 6×6 grid.
2. Place 12 seed tiles onto the plot (tap-to-place first — mobile-friendly;
   drag is a later nice-to-have, not v1).
3. Matching neighbouring tiles sprout, then bloom. Certain adjacencies can
   **mutate** into a brand new plant species (rare — this is the variable
   reward and the thing players chase and share).
4. Fill the plot. The finished plot appends **one row** to your permanent
   garden.
5. Come back tomorrow: a new plot, and your garden is one row bigger.

The game must be fun within 30 seconds of first play. If it isn't, the
onboarding is wrong, not the player.

## 3. The daily plot (no server required)

- The plot is **date-seeded**: today's date run through a deterministic
  pseudo-random generator produces today's plot. Same date + same formula
  = same plot for every player, with no server handing it out.
- Same puzzle for everyone is what makes it a shared daily ritual (and
  what makes sharing and comparing fair).
- One plot per day. Scarcity is the point: you cannot binge it, and
  missing a day is a real event (handled forgivingly — see §6).

## 4. Plants and the collection book

- **Base species:** ~24 at launch, grown from the seed tiles each day's
  plot provides. Players never buy plants.
- **Mutations:** when certain plants end up adjacent, there is a chance
  they combine into a new species the player has never seen. The moment a
  mutation happens, it is stamped **permanently** into the collection
  book (a Pokédex for plants). The collection book is the long-term
  chase: common species fill in fast, rare mutations take seasons.
- **Seasons:** 7-day seasons with a rotating biome. Each biome makes new
  species available. When a season closes, the player gets a shareable
  season postcard and carries one **heirloom tile** forward into the next
  season.
- **Practice plot:** an unlimited practice plot is available any time,
  but it grows nothing — no garden rows, no collection entries, no
  streak progress. It exists so new players can learn without pressure
  and so the daily scarcity is never frustrating.

## 5. The garden (the investment)

- The garden is the visible, permanent record: one row per day played.
- It is the progression layer. Other daily games reset to a streak
  number; LOAM accumulates into something you can look at.
- The bloom/mutation moment must feel satisfying (animation, sound
  optional) — this is the clip players will share.

## 6. Streaks — forgiving by design

Research is clear: a broken streak is when players quit. So LOAM never
destroys progress:

- **Mulch freeze:** every player starts with 1. Earn another every 7 days
  of tending. A freeze saves a missed day automatically.
- **Archive repair:** once a week, a 48-hour window to repair a missed day.
- **Season goal:** "tend 5 of 7 days", not perfection. A 5/7 season is a
  complete season.
- **Missed days grow wildflowers:** a missed day never leaves a hole in
  the garden. It grows wildflowers instead — visible, charming, and
  clearly not a punishment.

## 7. Social — without a server

- **Share:** a Wordle-style share artefact of your garden row (text/emoji
  grid, spoiler-free) that players can paste anywhere.
- **Friend codes:** a share link that encodes a garden row inside the URL
  itself (e.g. `?visit=...`), decoded client-side by the friend's
  browser. No server in the middle.
- **NO global leaderboard in v1.** Evidence says leaderboards motivate
  the top few percent and demotivate everyone below them. Friends-only
  comparison via share links is the social layer.
- **NO accounts, NO backend, NO multiplayer, NO chat, NO ads in v1.**

## 8. Progress storage (no accounts)

- All progress (garden rows, streak/season state, collection book) is
  stored in the browser's localStorage on the player's device.
- Same device + same browser = progress is there. A different device or
  browser starts its own garden.
- **Backup code:** the game provides a short code encoding the whole
  garden. The player saves it (screenshot/notes) and pastes it into any
  device to restore or move their garden. This is the no-account version
  of "log in somewhere else". Show it prominently in settings, and
  remind the player to save it.
- Accounts / cloud sync become worth it only later, if players ask to
  carry a garden across devices without thinking about backup codes.
  That is a backend decision for later, not a v1 feature.

## 9. Tech constraints (v1)

- Single `index.html` with CSS + JS, no framework, no build step, no
  external runtime dependencies.
- Mobile-first: tap-to-place, readable on a phone screen, playable in a
  browser tab with no install.
- Static hosting only (GitHub Pages). Nothing in v1 may require code
  running on a server.

## 10. Out of scope for v1 (do not build these yet)

Accounts, backend/database, global leaderboard, real-time multiplayer,
chat, user-created plots, ads, app-store wrapper, monetisation of any
kind. If a feature needs a server, it is not v1.

## 11. Build order — micro-steps for Claude Free (chat + paste workflow)

The builder (Claude) is on a **free tier** and works in **chat, not inside
VS Code**: Claude gives code in chat, and the builder pastes it into VS
Code by hand. That changes two things versus an integrated setup:

- Claude **cannot read repo files**. Prompts must be self-contained:
  every prompt carries the context it needs (game, step, rules). Do NOT
  rely on "read LOAM-DESIGN.md / index.html first" — chat Claude cannot
  see those files.
- **Keep one Claude conversation going** for the whole build, so Claude
  remembers the code from earlier steps. If a fresh conversation is ever
  needed (context gets confused or slow), paste the current `index.html`
  along with the next step's prompt so Claude is grounded again.

**One step = one Claude message. Never batch steps.** After each step,
open `index.html` in a browser, check the "Done looks like" line, then
refine with Claude if needed. If a step breaks something, ask Claude to
fix only that thing — do not re-ask for the whole step. If the free
limit is hit, stop: the work so far is safe in VS Code and (once pushed)
in the repo, and the next step waits for the reset. Pace: slow and
steady, piece by piece, refining each piece together before moving on.

### Step 1 — Shell + grid
Prompt: create `index.html` with the title LOAM and an empty 6×6 grid,
basic styling, mobile-readable.
Done looks like: open the file, see a 6×6 grid.

### Step 1b — Home screen (do this before step 2)
Players must not land straight on a bare grid. Prompt: add a home
screen shown first — LOAM title, tagline "Today's plot is ready.", one
line explaining the game ("Plant your 12 seeds. Whatever blooms stays
in your garden."), and a big button "Tend today's garden". The grid
stays hidden until the button is tapped. Change nothing else.
Done looks like: open the file, see the home screen first; tap the
button, the grid appears.

### Step 2 — Seeds + placement (may be combined with the wording fix
above into one Claude message, since both are tiny — but never combine
step 2 with step 3 or later)
Prompt: add a seed tray below the grid with 12 seeds: 4 sunflowers,
4 roses, 4 tulips. Tap a seed in the tray to select it (highlight it),
then tap an empty grid cell to plant it there. Tap a planted seed to
pick it back up into the tray. Show how many seeds are left to plant.
Keep all existing styling, the home screen, and the grid exactly the
same. Do NOT add blooming, garden rows, saving, or anything else yet.
Done looks like: you can place and remove seeds by tapping, and the
seeds-left counter counts down and back up.

### Step 2b — Taste pass (presentation only, logic untouched) — DONE
Botanical identity: cream paper (#f6f0dd), deep green ink (#22392c),
marigold accent (#e8a13d), Fraunces display font for the title
(Google Fonts link, Georgia fallback), three small inline SVG flowers
replacing emoji, seed tray styled as a labeled seed packet ("Today's
Seeds"). Further visual refinement is DEFERRED to step 12 polish —
do not chase perfect taste now; bank the improvement and keep building
mechanics.

### Step 3 — Bloom
Prompt: add blooming ONLY. Rule: after every placement or pickup,
recompute blooms from scratch. Any planted seed with an orthogonal
neighbour (up, down, left, right — NOT diagonal) of the same species
is bloomed. A bloomed seed gets a gold ring, grows slightly bigger,
with a small pop animation. Show a "Blooms: N" counter on the game
screen, where N = number of bloomed seeds. Picking a seed back up
recomputes blooms too, so neighbours that no longer match lose their
bloom. Do NOT add plot completion, garden rows, saving, or mutations
yet.
Done looks like: plant two matching seeds adjacent → both bloom with
gold ring + pop, counter goes up; pick one back up → bloom goes away,
counter goes down.

### Step 2 — Seeds + placement
Prompt: add a tray of 12 seed tiles. Tap a seed, then tap a cell, and the
seed is placed there. Tap a placed seed to pick it back up.
Done looks like: you can place and remove seeds by tapping.

### Step 3 — Bloom
Prompt: when two or more matching seeds end up adjacent, they bloom
(colour change + small animation + a bloom counter).
Done looks like: place two matching seeds next to each other, see them
bloom.

### Step 4 — Plot complete
Prompt: when all 12 seeds are placed, the plot is complete (see the
clarification below — complete means all 12 seeds placed, NOT all 36
cells filled; the 6×6 is the field you choose positions on).
Done looks like: placing the 12th seed shows a "plot complete" state.

### Step 5 — Garden row
Prompt: on plot complete, append a visual row to a garden section below
the plot (a mini snapshot of the finished plot).
Done looks like: complete a plot, the garden grows by one row.

### Step 6 — Save / load
Prompt: save the garden to localStorage on every change; load it on page
open. Add a backup-code display in a settings area (code encodes the
garden; pasting a code restores it).
Done looks like: reload the page, the garden is still there.

### Step 7 — Daily seed
Prompt: generate today's seed set from today's date with a deterministic
formula, so every player gets the same seeds on the same day. Show
today's date on the page.
Done looks like: the same seeds appear for the same date (check in two
browsers, or by reopening the page).

### Step 8 — Mutations + collection book
Prompt: add fixed mutation recipes (v1 uses fixed recipes, not chance —
see below), e.g. two specific species adjacent produce a named new
species. Add a collection book section listing every species grown, with
new mutations highlighted.
Done looks like: place a recipe combination, see the mutation and a new
collection entry.

### Step 9a — Streak + wildflowers
Prompt: track days tended. A day with no play gets a wildflower row in
the garden (never a hole). Show the current streak.
Done looks like: streak counts up; a missed day shows wildflowers, not
a gap.

### Step 9b — Forgiveness
Prompt: add 1 starting Mulch freeze (saves one missed day automatically),
earn one every 7 tended days, a once-a-week 48-hour Archive repair, and
a season goal of "tend 5 of 7 days".
Done looks like: a missed day with a freeze does not break the streak.

### Step 10 — Share
Prompt: add a copy button that produces a Wordle-style text/emoji grid
of your latest garden row, plus a friend-code link (`?visit=...`) that
encodes a garden row in the URL and is decoded on open.
Done looks like: copy gives pasteable share text; opening a friend link
shows their row.

### Step 11 — Seasons / biomes / postcard / heirloom
Prompt: 7-day seasons with a rotating biome (new species available per
biome); on season close, show a shareable season postcard and carry one
heirloom tile into the next season.
Done looks like: a season ends with a postcard and an heirloom carried
forward.

### Step 12 — Homepage shell + polish
Prompt: ONLY NOW build the homepage shell around the working game
(title, how-to, garden view, collection, share), plus visual polish and
sound (optional). Do not change game rules in this step.
Done looks like: the game looks finished, and still plays exactly as
before.

### Two v1 decisions (override these if you disagree)

- **"Fill the plot" = all 12 seeds placed**, not all 36 cells filled.
  The 6×6 grid is the field; placement strategy (adjacency for blooms
  and mutations) is the game.
- **v1 mutations use fixed recipes, not random chance.** Recipes are
  testable (you can trigger one on demand to verify it works) and fair
  for a shared daily puzzle. Chance-based mutation tuning is a
  post-playtest decision, not a v1 feature.

## 12. Playtest — how we know it is real (before scaling)

- 2-week playtest, ~10 friends, link sent ONCE, no reminders.
- GO if: ≥4/10 return unprompted on day 2; ≥3/10 still playing day 7;
  ≥2 unprompted shares; ≥6/10 can describe the loop in one sentence;
  zero unrecovered garden losses.
- KILL / PIVOT if: <3/10 return on day 2, or most players cannot
  explain the game. Then change the game, not the marketing.
- Retention context (directional only — these are mobile-game figures,
  not web-game figures): median games keep ~22% day 1 and ~4% day 7;
  top-quartile ~27% day 1 and ~7–8% day 7.

## 13. What makes LOAM different (say this in one breath)

Daily puzzle that is spatial and visual, not words or trivia
(language-independent); daily play accumulates into a visible garden,
not a streak number; social without a server; streaks that never destroy
progress.
