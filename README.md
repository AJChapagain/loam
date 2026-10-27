# LOAM — a daily garden game

**One line:** Every morning, every player gets the same 6×6 plot. Place 12 seed
tiles. Matching neighbours sprout, bloom, and sometimes mutate into a new
plant species. The finished plot becomes a row in your permanent garden.

- ~2 minutes a day, browser tab, no account, no server.
- Core loop: **Place tiles. Matching neighbours bloom. Fill the plot.**
- Tech: plain HTML/CSS/JS, no framework, no build step. Open `index.html`
  and play. (GitHub Pages can serve this repo later; nothing to install.)
- The full game spec lives in `LOAM-DESIGN.md`. That file is the source of
  truth — build from it, and update it when a design decision changes.

## Repo layout (planned)

- `index.html` — the game (to be built; single file first, split later only
  if it earns it)
- `LOAM-DESIGN.md` — game design spec
- `README.md` — this file

## Separation rule

This repo is **separate** from `AJChapagain/AJChapagain.github.io`
(ajchapa.com). Nothing in this repo touches the website, and nothing in the
website repo touches this game. If the game ever gets its own domain, that
is a later DNS/Pages decision, not a reason to merge repos.
