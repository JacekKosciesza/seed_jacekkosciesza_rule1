# seed_jacekkosciesza_rule1

Custom [TradingView Pine Seeds](https://github.com/tradingview/pine-seeds-docs) data:
Rule #1 **sticker / buy price bands** per company, consumed by the
`rule1-seed-bands.pine` indicator.

## Layout

```
data/<SYMBOL>.csv                 # EOD OHLCV, one file per company
symbol_info/seed_jacekkosciesza_rule1.json   # manifest (must match repo name)
rule1-seed-bands.pine             # the indicator that reads this feed
```

### CSV encoding (`YYYYMMDDT,open,high,low,close,volume`)

The four Rule #1 bands are packed into OHLC so one row carries the whole stack:

| field   | meaning                | derived in Pine        |
|---------|------------------------|------------------------|
| `open`  | Buy Price              | —                      |
| `close` | Sticker Price          | —                      |
| `high`  | 20% Above Sticker      | (= close × 1.2)        |
| `low`   | Buy Price (band floor) | —                      |

`20% Above Buy` is computed in Pine as `open × 1.2`. Volume is always `0`.

Symbols today: **ACN, EPAM, GOOGL, LULU** (monthly points, 2016→present,
EPS-anchored to each company's current Rule #1 buy price).

## Regenerate the data

From the private `invest` repo (needs `invest.db`):

```bash
node scripts/build-pine-seed.mjs ~/GitHub/seed_jacekkosciesza_rule1 seed_jacekkosciesza_rule1
```

Then commit & push this repo. TradingView re-syncs once per day (EOD).

## One-time TradingView onboarding

Pine Seeds data is **not live until TradingView connects the repo**:

1. Push this repo to GitHub **public**, named exactly `seed_jacekkosciesza_rule1`.
2. Follow the connect process in
   [`tradingview/pine-seeds-docs`](https://github.com/tradingview/pine-seeds-docs)
   (submit the repo for review). Approval takes a few days.
3. Once connected, the symbols resolve as `SEED_JACEKKOSCIESZA_RULE1:ACN` etc.,
   and `request.seed("seed_jacekkosciesza_rule1", "ACN", close)` returns data.

**Caveats:** EOD only (one sync/day), and the data is **publicly searchable**
on TradingView. Don't put anything sensitive here.

## Use the indicator

Load a covered symbol (e.g. `NYSE:ACN`), open the Pine Editor, paste
`rule1-seed-bands.pine`, **Add to chart**. Bands auto-render for any symbol in
the feed and stay blank otherwise.
