# token-radar-01

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/bayu-okto/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/bayu-okto/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Token Radar (radar-2) — a dashboard that ranks pre-token DeFi protocols by implied value and shows how to farm each one. Entry for the make.fun bounty **[Build the best token launch radar](https://make.fun/bounty/token-radar-01)**.

## The model

Every protocol on the snapshot's watchlist (TVL ≥ $50M, no token on record in DefiLlama) is scored, plus every rankable pre-token protocol below the floor — 226 rows in all. The implied value follows only from the snapshot's own figures:

- **Category multiple** — what the market already pays per dollar of TVL for protocols in the same category: the median market-cap/TVL of the category's live-token protocols (at least 3 comparables; thinner categories borrow the global median of 0.246), blended 75/25 with the global median and clamped to 0.05–1.50.
- **Growth** — the protocol's 7-day TVL change, clamped to −50…+150%, weighted 0.6.
- **Maturity** — how long it has run with no token on record (days since DefiLlama first listed it), 0.55–1.00 over 3 years.
- **Chain breadth** — +2% per extra chain, up to +10%.

`implied = round(TVL × category_multiple × growth_mult × maturity_mult × breadth_mult)`. No protocol name is special-cased; the dashboard prints every category multiple with its comparable count so the anchor can be checked.

Each row also carries a 7-step farm plan derived from the protocol's own record (category, main chain, TVL, age): a fresh wallet, the bridge onto its chain with chain-aware gas costs, the category's action, a weekly qualifying action, a hold window, a claim check, and a farm log. Live-token protocols are set aside with their market caps shown — the market's own value, not a model.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <path-to-snapshot.json>
```

Node 22.18+ (or Node 24), standard library only — nothing to install. The program reads the snapshot, writes `board.json`, `data.js`, copies `dashboard/` and `screenshots/` to the output directory, and writes `dossier.json` with the vendored make.fun library. Open `out/dashboard/index.html` in a browser to use the board.

---

<sub>Entered on [make.fun](https://make.fun/?ref=bayu-okto&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
