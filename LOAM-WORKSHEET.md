# LOAM — Game Worksheet (everything, to a T)

Living notes for LOAM. Updated 2026-10-27 evening. Also on GitHub:
AJChapagain/loam, `LOAM-WORKSHEET.md` (public repo). Code state: title
screen + garden screen, planting, growth, saving, weather marks, animated
weather, come-back message, coins, shop dock, drag-to-sell, TEST buttons.
Shop + selling POLISH still pending (prompt delivered 2026-10-27, awaiting
his Claude result). Still no daily claim, extra species, pick-up, or
friends yet.

## The game in one line

Grow a Garden-like browser game (online first, mobile decision later). Plant
seeds in a front-view garden bed, they grow over time, weather can mark them,
sell them later for coins, buy more seeds. One file: `index.html`.

## Where we're going

- **Direction:** browser game first, played online. Mobile is a decision
  AFTER the online version works and has been tested on a real phone.
  Current goal: test it and make it work — one mechanic per step, verified
  before moving on.
- **The loop when done:** plant seeds → they grow over real time → weather
  can mark blooms rare → sell roses for coins → buy more seeds → claim 1
  free seed a day → more flower species → friends (only if he decides what
  it is) → publish online → mobile decision.
- **How we build:** he runs Claude Free chat (not in VS Code), pastes the
  prompt + current code in one message; Claude returns the FULL updated
  index.html; buddy verifies the returned code by reading it before the
  next step. The website repo (AJChapagain.github.io) is NEVER touched for
  game work. `index.html` still lives only in his VS Code — getting it
  into the loam repo is part of the publish step.

## Screens

- **Title screen:** big bobbing LOAM logo (Fraunces, soil-stack text shadow),
  "Enter Garden" sunshine button. Sunny sky behind it: glowing sun, 3 drifting
  clouds, 3 green hills.
- **Garden screen:** mini LOAM logo (top), weather badge (top-left), TEST panel
  (top-right), "Your Seeds" packet, garden bed (bottom, grounded on the grass
  with a contact shadow), toast messages (bottom).

## The garden bed

- Front-view raised bed: trapezoid wood rim, soil surface, front wood board,
  contact shadow on the grass.
- **10 plots**, rows of 2, 3, 2, 3 (back to front). Back rows narrower, front
  rows wider = depth. Mounds: warm radial-gradient soil domes with a soft
  highlight. Pop-in animation (diagonal wave) when entering the garden.
- One plant per mound. Empty mounds get a dashed white ring while a seed is
  selected.

## Seeds & planting

- Start: exactly **1 rose seed**.
- Packet UI: round seed slot with a tiny rose-seed drawing, count badge,
  hint text ("Rose seed: tap to select" → "Now tap a soil mound" →
  "Packet empty"). Selected seed glows gold and lifts.
- Tap seed → tap empty mound → planted. Planted seed shows as a small seed
  sitting in the soil on that mound.

## Growth (testing times — lengthen later)

- 3 stages: **seed → sprout → full rose** (stem, leaves, flower head, drawn
  growing out of its mound, with a grow/pop animation on stage change).
- `SEED_MS` = 1 minute (seed stage), `SPROUT_MS` = 3 minutes (full rose after).
- Re-checked every 10 seconds (`CHECK_MS`) + on visibility change, so plants
  grow while the page is open and catch up on return.
- Real times (hours / a day) come later — the constants sit at the top of the
  file for tuning.

## Weather (one per day, same for every player)

- Picked from the date: the YYYY-MM-DD string is hashed to 0–99 and mapped
  to weights. Shown as a badge (icon + name) + a soft tint over the bed.
- `realWeather` = true date weather (used for rolling marks). `shown` =
  what the badge/tint show (TEST preview can override it for the session).

| Weather | Days | Tint | Mark chance on full bloom | Mark message |
|---|---|---|---|---|
| Sunny | 55% | none | — (nothing) | — |
| Rain | 20% | blue | 60% | "Your rose is Rain-kissed!" |
| Storm | 10% | dark | 40% | "Lightning struck your rose!" |
| Frost | 7% | icy pale | 50% | "Your rose is Frost-touched!" |
| Golden Hour | 3% | golden | 80% | "Your rose turned Golden!" |
| Ember heatwave | 5% | warm orange | 50% | "Your rose is an Ember rose!" |

- **Marks:** rolled ONCE when a rose reaches full bloom, using the real
  weather. Failed roll = classic rose. A mark is kept forever (including
  after reload) and shown with a toast message when it lands.
- **Looks** (recolors + decorations of the same rose art):
  - Classic: red rose (the "sun rose" — a rose grown on a Sunny day).
  - Rain-kissed: glossier, deeper red, white shine streaks, water droplets.
  - Lightning-struck (storm): golden rose, lightning bolts + sparkles.
  - Frost-touched: pale icy blue-white petals, frost crystals, icy rim.
  - Golden: glowing gold petals, radiant halo, gold sparkles.
  - Ember: deep orange-red petals, fiery rim, flame decorations, warm halo.
- **"Nothing at all" happens two ways:** Sunny days (55% of days) can never
  mark, and any weather day's roll can fail → classic rose.

## Animated weather (visible) + come-back message

- Two layers: `.sky-fx` behind the garden UI (sun rays, golden glow, storm
  sky, lightning flash) and `.fx` over the garden (rain, snow, embers).
  Both hidden until you enter the garden; both follow the SHOWN weather, so
  the TEST button previews each one.
- Sunny: slow-spinning sun rays. Golden Hour: bigger gold rays + warm glow.
  Rain: overcast + falling raindrops. Storm: dark sky + lightning flash +
  bolt that jumps to a random position + heavy rain. Frost: drifting
  snowflakes. Ember: glowing embers rising. Particle counts scale down on
  phones; reduced-motion players get no moving particles.
- Toast queue: messages queue up and show one at a time (4s each) instead
  of overwriting each other.
- Come-back message: when a seed is planted, and on load while plants are
  still growing, a toast tells the player to return — "Your rose is growing
  — come back later to see it bloom!" When the weather isn't Sunny it
  names it: "It's raining — come back later to see your blooms!" (the
  `say` text lives on each weather row).

## Saving

- Key: `loam.save.v2` (localStorage). Saved after every change (planting,
  buying, selling, TEST buttons, mark rolls).
- Saves: coin count + seed count + every plant (mound index, `plantedAt`
  timestamp, mark).
- On load: plants are rebuilt on their mounds at the correct stage for their
  age; seed count and packet restored; TEST buttons keep working.
- Validation: bad/missing data → fresh start (1 rose seed, empty bed). Seeds
  must be an integer 0–999; mound index must exist; mark must be a known
  look; `plantedAt` is clamped to now (no future plants).

## TEST buttons (remove later — testing only)

- **Next stage:** advances every planted plant one growth stage instantly.
- **Full bloom:** jumps every planted plant straight to a full rose instantly.
- **Next weather:** cycles Sunny → Rain → Storm → Frost → Golden Hour →
  Ember → Sunny. Display-only preview: updates badge + tint + the look of
  fully grown roses, but does NOT change saved marks, does NOT show the
  mark message, and reload restores true saved marks.

## VALUES — the economy (LOCKED, now in code)

Coins, shop, and selling are in the game since 2026-10-27 (shop/sell polish
pending). These values are locked here so every step uses them exactly. Rose
seed shop price is set so a classic rose sells for 2× its seed price; marked
roses climb with rarity (rarity = day weight × mark chance).

| Item | Value (coins) | Rarity (share of full blooms) |
|---|---|---|
| Rose seed (shop price) | 10 | — (bought, not grown) |
| Classic rose ("sun rose") | 20 | always (Sunny days + failed rolls) |
| Rain-kissed rose | 30 | 20% × 60% = 12% |
| Lightning-struck rose (storm) | 50 | 10% × 40% = 4% |
| Frost-touched rose | 60 | 7% × 50% = 3.5% |
| Ember rose | 80 | 5% × 50% = 2.5% |
| Golden rose | 100 | 3% × 80% = 2.4% (rarest) |

- Sell rule (for the selling step): a grown rose sells for its value above;
  selling frees its mound. A marked rose is worth its marked value, not the
  classic value.
- Coins start: 50 coins.
- Growth speed and value are NOT affected by weather in this version —
  weather changes looks only, for now.

## NOT built yet (in order, one step at a time)

1. Shop + selling POLISH (queued — see below; the first version is sloppy).
2. Daily free seed claim (1/day, can't double-claim).
3. More flower species (daisy, lavender, orchid, ...).
4. Picking plants back up (later, if wanted).
5. Friends — UNDECIDED, parked (see below).
6. Real growth times (hours / a day) + polish pass (sway, bed details).
7. Publish online (GitHub Pages) for real phone testing.
8. Mobile decision (after online works).
9. TEST buttons removed before any public release (they're testing-only).

## Ideas (parked here until he says build it)

- Friends: add a friend, see their garden, maybe trade — UNDECIDED, needs
  the share-code vs backend decision first (see Friends section).
- More species: daisy, lavender, orchid named so far — no stats, prices, or
  looks decided yet.
- Picking plants back up: reclaim a growing plant as a seed — wanted at
  some point, no design yet.
- Garden polish: swaying plants, extra bed details — after the loop works.
- Nothing here gets built until he says so. Ideas go in this section first,
  then become roadmap steps.

## Coins + seed stand + selling (BUILT 2026-10-27 ~16:03, needs polish)

- Coins: start 50, pill next to the weather badge, saved in `loam.save.v2`
  (old saves without coins default to 50). Seed price 10, sell values as
  locked above. Buy works (deducts 10, +1 seed, toast, saves).
- DEVIATION from the spec: Claude built DRAG-to-sell instead of the sell
  bar — a permanent floating "Shop" dock with a dashed dropzone, a 🌹
  emoji drag ghost, and hover tooltips. Tapping a grown rose does NOTHING
  visible (dead `selectedRose` state, no sell bar was built), so keyboard
  and tap-only players cannot sell at all. Tooltip shows a WRONG
  "estimated value" (20) for growing plants whose mark isn't rolled yet,
  and tooltips are hover-only (useless on phones). Coins pill uses a fixed
  left offset and can overlap long weather names ("Golden Hour"). Seed row
  is text-only, no seed art. Also a typo: `*background*` in the seed-slot
  transition.
- POLISH queued (prompt delivered 2026-10-27): cut drag-to-sell entirely,
  Shop button + panel instead of the floating dock, tap sell bar showing
  the real rose drawing + name + value + Sell button, price list in the
  shop, no emoji rose stand-ins, weather + coins in one proper top row,
  no value shown before bloom, fix the typo. Economy numbers unchanged.

## Friends (parked — his call, not designed yet)

- What he wants, loosely: add a friend, see what they have (their garden),
  maybe trade. He said "idk yet, we will see" — so this is NOT specced.
- What it needs before it can happen: the economy first (this step), then
  a decision on HOW friends work with no server — friend share codes (like
  a `?visit=` link that shows a read-only garden) vs a real backend.
  Trading would also need rules (what can be traded, fair values — the
  value table above already gives every rose a price, which helps).
- Do not build friends until he decides what it is.
