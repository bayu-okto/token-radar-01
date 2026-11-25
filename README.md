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

## Methodology

The board is a yardstick, not a forecast. It answers one question: if this protocol launched a
token tomorrow and the market priced it the way it already prices the live-token protocols in its
own category, what would that token be worth? Nothing in the score says a token is coming, when
it would arrive, or what it would trade at on day one. It is a way to compare 226 protocols on
the same footing, using only the snapshot's own figures.

### The five inputs

Every row is the product of five numbers, all taken from the snapshot:

- **TVL** — the protocol's total value locked, in dollars. This is the size of the thing being priced.
- **Category multiple** — what the market already pays per dollar of TVL for protocols in the
  same category: the median market-cap/TVL of the category's live-token protocols. A category
  with at least three live comparables is priced at a 75/25 blend of its own median and the
  global median; a thinner category borrows the global median outright. Every multiple is
  clamped to 0.05–1.50, so no thin or wild category can set an absurd price.
- **Growth multiplier** — the protocol's seven-day TVL change, clamped to −50…+150% and weighted
  0.6, so a hot week lifts the score but cannot double it on its own.
- **Maturity multiplier** — how long the protocol has run with no token on record, from 0.55 for
  a brand-new listing to 1.00 after three years. Time in the wild without launching is the
  closest thing a pre-token protocol has to a track record.
- **Breadth multiplier** — +2% per extra chain, up to +10%. Each additional chain reaches more
  farmers, but the tenth chain adds far less than the second, so the bonus is capped.

`implied = round(TVL × category_multiple × growth_multiplier × maturity_multiplier × breadth_multiplier)`,
with a floor of $1: no ranked row ever prices at zero. Every multiplier is rounded to four
decimals before it multiplies, so anyone can reproduce the board's number exactly from the
figures the dashboard prints.

### Why the anchor is the category, not the market

A lending protocol and a bridge do not sell the same thing, so they should not be priced by the
same yardstick. The market already tells us what it pays per dollar of TVL in each category —
the live-token protocols are that verdict, written in real money. Anchoring to the category
median keeps the comparison honest: a protocol is measured against its own kind, not against
the hottest category of the month. The 75/25 blend with the global median is a hedge against
thin categories: when only a handful of protocols trade in a category, their median is noisy,
and the global median steadies it. The clamp exists for the same reason — no thin or wild
category may set an absurd price.

### A worked example: Steakhouse Financial

Steakhouse Financial sits at rank 1 with an implied value of $568,603,850. Its category, Risk
Curators, has only one live-token comparable, so it borrows the global median of 0.246. Its
seven-day TVL change is −2.1935%, giving a growth multiplier of 1 + (−2.1935 / 100) × 0.6 =
0.9868. It has been listed for 543 days, so its maturity multiplier is 0.55 + (543 / 1095) ×
0.45 = 0.7732. It runs on 11 chains, so its breadth multiplier is 1 + 0.02 × min(10, 5) = 1.10.
Putting it together:

`2,753,981,495 × 0.246 × 0.9868 × 0.7732 × 1.10 = 568,603,850`

The same arithmetic reproduces every row on the board. The next two rows are priced by the same
global anchor — Gauntlet at $341,286,097 and Veda at $325,527,040 — because their categories are
thin too. That is the model's honest admission: where a category has too few live protocols to
price itself, the whole market's median does the pricing.

### How to read a farm plan

Each row carries a seven-step plan built from the protocol's own record — its category, its main
chain, its TVL and its age. The steps are: a fresh wallet, the bridge onto the chain, the
category's core action, a weekly qualifying action, a hold window, a claim check, and a farm
log. Capital scales with TVL ($50–$250) and the hold window with age (30–60 days), so a young
protocol gets a longer qualifying window than an old one. The plan is a checklist, not advice:
gas costs are the chain's own, and the claim step is to use only the link the protocol itself
publishes.

### What the model cannot see

The snapshot is a single day. TVL can move fast, and it can be inflated. The model cannot see a
team, an audit, a token design, or whether a protocol even intends to launch — and it does not
try. A high implied value means the protocol is large and its category is well priced; it does
not mean a token is coming, and it is not a price target. Run the program twice on the same
snapshot and the board is byte-identical: the model is deterministic, so any disagreement is
about the inputs, not the arithmetic.

### Reproducing the board

The program is deterministic: the same snapshot always produces the same board, byte for byte.
To check the numbers in this repository, run the scorer against the snapshot the bounty shipped
and open the dashboard it writes — the top rows should match the board shown here exactly:

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <path-to-snapshot.json>
```

Every multiplier the model uses is printed in the dashboard — the category table under "How
implied value is computed", the per-row equation in each detail drawer, and the Score Lab's
step-by-step multiplication — so the worked example above can be repeated for any row, not just
the first. If a recomputed row disagrees with the board, the data beside the page is stale; the
model itself has no randomness to hide behind.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <path-to-snapshot.json>
```

Node 22.18+ (or Node 24), standard library only — nothing to install. The program reads the snapshot, writes `board.json`, `data.js`, copies `dashboard/` and `screenshots/` to the output directory, and writes `dossier.json` with the vendored make.fun library. Open `out/dashboard/index.html` in a browser to use the board.

---

<sub>Entered on [make.fun](https://make.fun/?ref=bayu-okto&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
