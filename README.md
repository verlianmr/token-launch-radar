# token-launch-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/verlianmr/token-launch-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/verlianmr/token-launch-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar: 40 watchlist protocols plus 160 early pre-token protocols, ranked by
implied value. Each category is priced by the live market — the median mcap/TVL of its
live-token protocols, shrunk toward the global median — not by a flat multiple. Every row
carries a concrete seven-step farm plan with costs and times. The dashboard is a static,
no-network page: sortable, filterable, with a per-protocol detail view that shows the model's
own arithmetic.

## The model (radar-2)

```
category_multiple = median mcap/TVL of the category's live-token protocols,
                    shrunk toward the global median with a prior weight of 3 comps.
                    A category needs at least 3 live comps; with fewer it takes the
                    global median. Clamped to [0.05, 3.0].
implied = round( tvl_usd x category_multiple
                 x (1 + clamp(change_7d, -60, 120) / 100 x 0.5)
                 x (0.6 + min(age_days, 1095) / 1095 x 0.4)
                 x (1 + min(chains - 1, 9) x 0.02) )
```

Everything follows from the snapshot's own figures. No protocol name is special-cased.

## Run it

```sh
# With the competition kit's sealed snapshot:
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <path-to>/snapshot.json
# Writes out/board.json, out/data.js, out/dashboard/, out/screenshots/ and out/dossier.json.
# Node 22+, standard library only, no install.
```

Open `out/dashboard/index.html` (with `out/data.js` beside it) to see the board.

## Layout

- `main.ts` — the entry: reads the snapshot, writes the board, the dashboard data and the dossier.
- `lib/model.ts` — the radar-2 model, the farm plans and the board builder.
- `dashboard/` — the static page: `index.html`, `app.css`, `app.js`. No network.
- `screenshots/` — the board, a detail view and a filtered view.
- `vendor/makefun-board.ts` — make.fun's dossier library, copied from the kit.

---

<sub>Entered on [make.fun](https://make.fun/?ref=cw40tr4n&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
