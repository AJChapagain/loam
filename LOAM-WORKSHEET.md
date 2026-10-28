# LOAM — Game Worksheet (everything, to a T)

Living notes for LOAM. Updated 2026-10-28 night. Also on GitHub:
AJChapagain/loam, `LOAM-WORKSHEET.md` (public repo). Code state: title
screen + garden screen, planting, growth (a day: sprout 12h, bloom 24h),
saving, stacking weather effects, animated weather, come-back message,
coins, Loam Florist storefront (left side, grounded; bottom sheet on
phones; compact rows), drag-to-sell (the plant itself drags now, no
emoji ghost), sway animations, TEST buttons, visible save system (Saved
flash + export/import codes). 5 species: Rose, Sunflower, Lavender,
Orchid, Moonflower — ALL available in the shop at all times (the daily
rotation was removed 2026-10-28 at his call). Each species is its own
size (sunflower tallest, lavender slimmest) and all five blooms were
revamped 2026-10-28 (layered petals, sunflower rays, fuller lavender
spike, second orchid bloom, brighter moonflower glow + sparkles). The
seed packet is a bottom bar now, under the bed instead of on top.
`index.html` lives IN the loam repo (pushed by buddy, with his fixes).
Still no daily claim, pick-up, or friends yet.

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
  effects stack on roses over days → sell roses for coins → buy more
  seeds → claim 1 free seed a day → more flower species → friends (only
  if he decides what it is) → publish online → mobile decision.
- **How we build:** he runs Claude Free chat (not in VS Code), pastes the
  prompt + current code in one message; Claude returns the FULL updated
  index.html; buddy verifies the returned code by reading it before the
  next step. Since 2026-10-27 night buddy also fixes the code DIRECTLY
  when he says "you fix it" (first direct fix: We pay removal + tooltip
  worth + multiplicative values). The website repo (AJChapagain.github.io)
  is NEVER touched for game work. `index.html` now lives in the loam repo
  (pushed 2026-10-27 night) as well as in his VS Code.

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

## Species (5 plants since 2026-10-28 — buddy's picks, a ladder)

Each seed grows into a visibly different plant (own bloom art, own classic
colors). Every plant uses the same petal classes, so weather palettes
recolor any species, and values multiply the same way (base × effect
multipliers). Old saves migrate: plants without a species become roses;
old single-number seed counts become rose-only counts.

| Seed | Shop price | Classic value | Look |
|---|---|---|---|
| Rose | 10 | 20 | red round bloom (the starter) |
| Sunflower | 25 | 50 | big yellow bloom, brown center |
| Lavender | 40 | 80 | purple spike |
| Orchid | 75 | 150 | pink exotic bloom, arching stem |
| Moonflower | 120 | 240 | white glowing bloom |

- **Daily rotation (REMOVED 2026-10-28, his call):** all five seeds are
  available in the shop all the time now — no featured seed, no waiting
  for a day, no greyed rows.
- The packet holds one slot per species (compact 40px slots with count
  badges); tap a slot to select it, tap a mound to plant.
- **Packet position (moved 2026-10-28, his call):** the seed bar sits at
  the BOTTOM of the garden screen, under the bed, instead of on top.
- **Unique sizes (2026-10-28):** every species is its own size via
  `.sp-*` classes — Sunflower 115% (tallest), Moonflower 108%, Orchid
  100%, Rose 92%, Lavender 62% (slimmest).
- **Bloom revamps (2026-10-28):** all five blooms redrawn richer — Rose
  gets a back petal ring + a second petal fold; Sunflower gets 8 ray
  petals + a third leaf; Lavender gets a fuller spike (bottom buds +
  tiny floret dots) + a third blade; Orchid gets a second smaller bloom
  lower on the arch + a bud + an extra side petal; Moonflower gets a
  double glow halo + 4 sparkles + a second highlight.
- **Drag = the plant itself (since 2026-10-28):** dragging lifts the real
  plant element off its mound and it follows the pointer (tilted, with a
  shadow). No more 🌹/🌈 emoji ghost. Dropping outside the shop puts the
  plant back on its mound; dropping on the shop sells it. The 10-second
  re-check skips a plant mid-drag so it can't crash.

## Growth (REAL times since 2026-10-28 — a day to grow)

- 3 stages: **seed → sprout → full bloom**.
- `SEED_MS` = 12 hours (seed stage), `SPROUT_MS` = 24 hours (full bloom
  after a day). Plant today, sprout tonight/tomorrow, bloom tomorrow.
- Re-checked every 10 seconds (`CHECK_MS`) + on visibility change, so
  plants grow while the page is open and catch up on return.
- TEST buttons (Next stage / Full bloom) still jump stages instantly for
  testing — they are testing-only and get removed before public release.
- Weather effects only land on FULLY GROWN plants, on later days — a
  plant that blooms tomorrow starts rolling for effects the day after.

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

- **Effects STACK (new system, prompt delivered 2026-10-27 night):** a rose
  blooms as a plain Classic rose. After that, every day's weather can ADD
  an effect — never replace, never recolor. The base rose always stays
  classic red; each effect only adds its decoration: Rain = droplets +
  shine, Storm = bolts + sparkles, Frost = crystals, Golden = gold
  sparkles + halo, Ember = flames + warm halo. All five can sit on one
  rose at once, decorations layered together.
- **Daily rolls:** once per day, each fully grown rose missing that day's
  weather effect rolls its chance (Rain 60, Storm 40, Frost 50, Golden 80,
  Ember 50; Sunny = nothing). Rolls happen at midnight while open + catch
  up on load for missed days (max 30), using each missed day's real
  date-weather, only for roses already grown by then; missed-day gains
  arrive as ONE summary toast. Seeds/sprouts never gain effects.
- **Names grow:** effects in the order they landed + plant name
  ("Rain-kissed Lightning-struck Sunflower"). No effects = Classic. All
  five = Rainbow (rainbow petals, value = base × ~10.4 — see the economy).
- **Values MULTIPLY, kept relative (REBALANCED 2026-10-28):** base × one
  multiplier per effect — Rain ×1.2, Storm ×1.5, Frost ×1.6, Ember ×1.8,
  Golden ×2.0. All five together ≈ ×10.4, so a Rainbow rose is 207 (was
  4500) and a Rainbow moonflower is 2,488 (was 67,500). One function
  (`plantValue`) feeds selling, the tooltip, and the sale reveal, so the
  number is always the same everywhere.
- **Old system replaced:** no mark rolled at bloom, no full-rose recolors,
  no "Next weather" look swap (preview now layers the decoration on top,
  display-only). Old saves migrate: single mark → effects list.
- **Chance is per-rose (Grow a Garden rule):** every rose rolls its own
  chance each day — some catch, some don't, never all-or-nothing. A whiff
  is visible ("No rose gained ..." toast) so a miss feels real.
- **Mystery values — REVISED 2026-10-27 night (his call, fixed by buddy
  directly):** the "We pay" board is GONE entirely — the shop lists
  nothing about values. The drag offer never previews a price ("Drag a
  bloom here to sell"). BUT hovering a grown rose DOES show its effects
  + what it is worth (he reversed the earlier no-value tooltip). The sale
  itself still reveals the amount (coin pop + sold toast).
- **The rose itself (redesigned 2026-10-28, buddy direct fix):** bigger,
  rounder bloom — overlapping round petals (left, right, top, cup,
  center) with a spiral middle and a highlight, on a stem with two
  leaves. The old small bloom read as weird/squat; this one reads as a
  rose.
- **Effects recolor the WHOLE rose (2026-10-28, replaces the old
  "decorations only, never recolor" rule):** each effect has a full
  petal palette, and the NEWEST effect landed sets the rose's colors —
  Frost turns the whole rose icy pale blue/white (plus a frosty veil
  over the bloom and crystals on the bloom, stem, AND leaves — ice gets
  the whole thing, including frosted leaves/stem); Storm turns petals
  electric violet with lightning bolts all AROUND the bloom (top,
  sides, bottom); Golden turns petals gold; Ember turns petals fiery;
  Rain turns petals a deeper wet rose. A Rainbow rose (all five) gets
  rainbow petals (red, orange, yellow, green, blue, purple). All
  effects' decorations still layer together, so stacking stays visible
  even though the newest effect owns the colors.
- **Phone layout (fixed 2026-10-28):** everything now stacks relative
  to the space. The weather/coins/save badges sit in the normal flow
  (no more floating HUD overlapping the logo); the shop panel is a
  bottom sheet (sits above the bottom edge, never covers the bed,
  scrolls if the space is short); the Shop tab hangs on the left edge
  vertically centered on the garden; the seed packet shrinks instead of
  overflowing; TEST buttons shrink on phones.
- **Layout REVAMP (2026-10-28, his call — the screen looked weird on
  phone AND PC, shop/coins invisible, right side off):** the bug was
  real — the bed's width formula used (100dvh - 300px) x 1.3, but the
  bed's height is width / 0.66, so the bed came out ~2x too tall and
  pushed the screen off the bottom (clipped silently, no scroll). Fixed:
  bed width = min(100%, 470px, max(140px, (100dvh - 400px) x 0.66)) — the
  x0.66 IS the bed's aspect ratio, so its height always fits; desktop
  bed flexes beside the fixed shop (flex: 1 1 0 + min-width: 0) so it
  can never overflow the stage and clip the shop left / bed right; HUD
  in normal flow on ALL screens (was absolute on desktop); pinch zoom
  disabled (maximum-scale=1, user-scalable=no) so phones can't zoom the
  layout into a broken state; garden screen scrolls vertically as a
  safety net so content is reachable, never silently clipped.
- **Visible save system (built 2026-10-27 night):** auto-save already
  exists (localStorage `loam.save.v2`) but the player couldn't tell.
  Added: a "Saved ✓" flash on every save + a Save button (near the coins
  badge) opening a panel with last saved time, Export (copies the whole
  save as one code) and Import (pastes a code back, validated like a
  load, confirm-first since it replaces the garden).
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
| Classic rose | 20 | always (Sunny days + failed rolls) |
| Rain-kissed rose | 24 | 20% × 60% = 12% |
| Lightning-struck rose (storm) | 30 | 10% × 40% = 4% |
| Frost-touched rose | 32 | 7% × 50% = 3.5% |
| Ember rose | 36 | 5% × 50% = 2.5% |
| Golden rose | 40 | 3% × 80% = 2.4% (rarest) |

- Sell rule: a grown plant sells for its value; selling frees its mound.
  Values MULTIPLY (rebalanced 2026-10-28): base × Rain 1.2 × Storm 1.5 ×
  Frost 1.6 × Ember 1.8 × Golden 2.0; all five effects ≈ ×10.4 (Rainbow
  rose 207, Rainbow moonflower 2,488).
- THE RULE (2026-10-28): classic value = 2 × seed price, so one classic
  sale buys about one seed of the NEXT tier (rose 20 → sunflower seed
  25, sunflower 50 → lavender 40, lavender 80 → orchid 75, orchid 150 →
  moonflower 120). No single plant — even a Rainbow — buys out the
  shop, so progression survives; the 10-plot bed caps daily income.
- Coins start: 50 coins.
- Growth speed and value are NOT affected by weather in this version —
  weather changes looks only, for now.

## NOT built yet (in order, one step at a time)

Shop + selling polish: DONE 2026-10-27 ~20:59 via his own Claude pass —
Loam Florist storefront (sign, awning, window, OPEN sign, door dropzone,
coin pop, shop shake), sway animations; drag-to-sell KEPT. This supersedes
the earlier sell-bar polish prompt (never applied).

Weather effects STACK: BUILT 2026-10-27 night (code pasted) — effects list,
daily rolls, missed-day catch-up (max 30 days), Rainbow rose, "We pay"
board, "Apply weather" TEST button.

Mystery values + left-side shop + visible save system: DONE 2026-10-27
night — "We pay" board REMOVED entirely (buddy's direct fix, his call),
tooltip now shows effects + worth, values MULTIPLICATIVE (Rainbow =
4500), left-side grounded shop + Save panel (Saved flash + export/import
codes) all in the code. `index.html` PUSHED to AJChapagain/loam by buddy
same night.

Rose redesign + phone layout: DONE 2026-10-28 night (buddy direct fix) —
bigger rounder bloom, effect palettes recolor the whole rose (newest
effect wins colors; frost ices bloom + stem + leaves; lightning bolts
encircle; rainbow petals), phone HUD in flow + shop bottom sheet + Shop
tab vertically centered. Pushed to AJChapagain/loam same night.

5 species + daily rotation + compact shop + drag-the-plant-itself +
day-long growth: DONE 2026-10-28 night (buddy direct fix) — Sunflower /
Lavender / Orchid / Moonflower join the Rose (ladder 10→120 seed price,
20→300 classic value); one new seed featured per day in the shop, others
greyed with return day; drag lifts the real plant (no emoji ghost);
growth is now 12h sprout / 24h bloom. Pushed to AJChapagain/loam same
night.

All seeds available + bloom revamps + unique sizes + bottom seed bar:
DONE 2026-10-28 night (buddy direct fix) — rotation REMOVED, every seed
buyable any time; all five blooms redrawn richer; each species its own
size (Sunflower 115%, Moonflower 108%, Orchid 100%, Rose 92%, Lavender
62%); seed packet moved from the top to a bottom bar under the bed.
Pushed to AJChapagain/loam same night.

Economy rebalance + layout revamp: DONE 2026-10-28 night (buddy direct
fix) — classic = 2x seed price, effect multipliers cut to Rain 1.2 /
Storm 1.5 / Frost 1.6 / Ember 1.8 / Golden 2.0 (Rainbow ≈ 10.4x, was 225x:
Rainbow moonflower 2,488, was 67,500); bed height math fixed (x0.66, was
x1.3 — the bug that pushed the screen off the bottom), desktop bed
flexes beside the shop, HUD in normal flow everywhere, pinch zoom
disabled, garden screen scrolls as a safety net. Pushed to
AJChapagain/loam same night.

1. Daily free seed claim (1/day, can't double-claim).
2. Picking plants back up (later, if wanted).
3. Friends — UNDECIDED, parked (see below).
4. Polish pass (sway, bed details).
5. Publish online (GitHub Pages) for real phone testing.
6. Mobile decision (after online works).
7. TEST buttons removed before any public release (they're testing-only).

## Ideas (parked here until he says build it)

- Friends: add a friend, see their garden, maybe trade — UNDECIDED, needs
  the share-code vs backend decision first (see Friends section).
- More species: daisy was named but is NOT built yet — lavender, orchid,
  sunflower, and moonflower shipped 2026-10-28.
- Picking plants back up: reclaim a growing plant as a seed — wanted at
  some point, no design yet.
- Garden polish: swaying plants, extra bed details — after the loop works.
- Nothing here gets built until he says so. Ideas go in this section first,
  then become roadmap steps.

## Coins + seed stand + selling (BUILT 2026-10-27, storefront since ~20:59)

- Coins: start 50, pill next to the weather badge, saved in `loam.save.v2`
  (old saves without coins default to 50). Seed price 10, sell values as
  locked above. Buy works (deducts 10, +1 seed, toast, saves).
- Selling = DRAG a grown plant into the shop (his choice — kept over the
  sell-bar idea). Shop is now the "Loam Florist" storefront: sign with
  rose logo, red/cream awning, seed window with compact rows (one per
  species: icon + name + price + Buy, or greyed with its return day),
  door dropzone with OPEN sign, coin pop + shop shake on sale. The
  dropzone shows only "Drag a bloom here to sell" — no price preview.
  The "We pay" board was REMOVED 2026-10-27 night (his call); hovering
  a grown plant shows its effects + worth instead. The shop was made
  SMALLER 2026-10-28 (narrower dock, smaller sign/logo/awning, compact
  rows, shorter door) so the 5 seed rows fit beside the bed.
- Extra life added in the storefront pass: roses sway (storm sways
  harder, ember sways warm), shimmer crystals, dripping rain droplets,
  twinkling halos. The `*background*` typo was fixed in that pass.
- NOTE: the sell-bar polish prompt from 2026-10-27 was SUPERSEDED and never
  applied — he had Claude beautify the drag storefront instead.
- Still-open gaps from the old critique (parked, not the current fight):
  coins pill fixed offset can overlap "Golden Hour", drag ghost is still
  an emoji (now varies by last effect: 🌈/🌼/🥀/🌺/🌹) instead of real rose
  art, tapping a grown rose still does nothing visible (keyboard players
  still can't sell).

## Friends (parked — his call, not designed yet)

- What he wants, loosely: add a friend, see what they have (their garden),
  maybe trade. He said "idk yet, we will see" — so this is NOT specced.
- What it needs before it can happen: the economy first (this step), then
  a decision on HOW friends work with no server — friend share codes (like
  a `?visit=` link that shows a read-only garden) vs a real backend.
  Trading would also need rules (what can be traded, fair values — the
  value table above already gives every rose a price, which helps).
- Do not build friends until he decides what it is.
