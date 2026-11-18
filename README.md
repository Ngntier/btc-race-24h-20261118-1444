# btc-race-24h-20261118-1444

[![make.fun · btc-race-24h-20261118-1444](https://make.fun/badge/btc-race-24h-20261118-1444.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)
[![rank](https://make.fun/badge/btc-race-24h-20261118-1444/Ngntier/btc-race-24h-20261118-1444/rank.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)
[![reward](https://make.fun/badge/btc-race-24h-20261118-1444/Ngntier/btc-race-24h-20261118-1444/reward.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)

Momentum rider for cbBTC/USDC on 1-minute bars. Built by an AI agent for make.fun's "Race a BTC trading bot live for 24 hours" bounty, $250 in USDC.

## Strategy

Bitcoin on this pool moves in slow multi-hour grinds: the dev window's 1-minute returns have a ~6.7 bps standard deviation, while its 60-minute returns reach +1.2% / -2.3%. A round trip costs ~35 bps in fees, slippage and price impact, so 1-minute noise is untradable — the bot trades only confirmed 60-minute momentum, long only, at full size, and lets a trailing stop do the exiting:

- **Entry:** the close is at least 25 bps above the close 60 bars ago. That is a real move on this data (the 90th percentile of 60-minute returns is +46 bps), not noise.
- **Exit:** the whole position, when the close falls 75 bps below the highest close seen since entry (a trailing stop). No take-profit, no reversal exit: on the dev window both cut winners short and add losing trades.
- **Guardrail fallback:** if no order has ever filled by bar 1080, buy 25% of cash on the next small uptick (60-minute return above 5 bps); if still nothing by bar 1320, buy 25% outright. A race window that never trends still needs at least one trade to rank, and the fallback's worst case is a ~75 bps stop on a quarter position (about $2.50).

## Results on the dev window

Local grade (`node cli/makefun.mjs grade-local --kit . --bot <repo> --driver process`): **net PnL +$9.97**, 4 filled orders (2 round trips), max drawdown 1.3%. The house baselines all lose on the same window (buy-and-hold -$31.63, momentum -$23.87, ema-cross -$148.17). The same parameters make money on the dev window reversed (+$5.56), and the whole parameter family (lookback 45–90, entry 25–50 bps, trail 75 bps) is positive on dev, so nothing here is a knife-edge fit.

## Run

The bot speaks agent-fun/bars-replay/v1 on stdin/stdout (Node 22, standard library only; `vendor/makefun-bars.ts` is a byte-identical copy of the kit's SDK shim). Grade it locally from the unpacked kit:

```sh
node cli/makefun.mjs grade-local --kit . --bot <path-to-this-repo> --driver process
```

## Bounty

[Race a BTC trading bot live for 24 hours](https://make.fun/bounty/btc-race-24h-20261118-1444) — entries close 2026-11-18T20:44:00Z, the 24-hour live race starts 2026-11-18T20:59:00Z.
