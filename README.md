# gto-equity-db

A precomputed range-vs-range equity database for NLHE flops, plus **GTO Equity
Explorer** — a zero-dependency web app for browsing it.

## The app

`index.html` is a self-contained single-page app (no build step, no dependencies)
that reads the JSON files sitting next to it.

**Run it locally** — it fetches JSON, so it needs to be served over HTTP:

```sh
python3 -m http.server 8000
# then open http://localhost:8000
```

Or enable **GitHub Pages** for this repo (deploy from branch, root folder) and it
works as-is.

Features:

- **Flop explorer** — type a flop (`AhKs7d`, `as ks 7d`, `10h 9s 8c` all work),
  tap the card slots, or roll a random one. You get hero range equity and nut
  advantage for all 20 preflop matchups, plus a 13×13 hand-class equity heatmap
  for any matchup (blue = hero-favorable, red = villain-favorable, gray midpoint
  at 50%).
- **Flop browser** — all 1,755 canonical flops for a chosen matchup, sortable by
  equity, filterable by texture (paired, monotone, connected, …) and searchable.
  Click a row to jump into the explorer.
- Shareable URLs (flop + matchup live in the hash), dark/light theme, tooltips
  everywhere, matchup files loaded on demand and cached.

## The data

| File | Contents |
|---|---|
| `lookup.json` | Maps all 22,100 flop keys → 1,755 canonical (suit-isomorphic) keys |
| `summary.json` | Per canonical flop, per matchup: `e` (hero range equity), `n` (nut advantage `h`/`v`/`n`), `hh`/`vh` (hero/villain range sizes) |
| `{matchup}.json` | Per canonical flop: `{hand_class: equity}` for hero's hand classes vs villain's range |
| `meta.json` | Generation parameters |

- 20 matchups: 9 single-raised pots (e.g. `BTN_vs_BB_srp`) and 11 3-bet pots
  (e.g. `BB3b_vs_BTN`). **Hero is always the preflop aggressor** (the open
  raiser in SRPs, the 3-bettor in 3-bet pots).
- Flop keys are the three cards sorted by rank descending (suit ascending
  `c<d<h<s` breaks ties), e.g. `AhKs7d`, `9c8d6d`.
- Hand classes with zero remaining combos (e.g. `22` on a `2c2d2h` board) are
  omitted from the matchup files.
- Equities were computed with [phevaluator] over 50 runout samples per hand.

[phevaluator]: https://github.com/HenryRLee/PokerHandEvaluator
