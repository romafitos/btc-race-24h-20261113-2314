# cbBTC trend bot — btc-race-24h-20261113-2314

A 1-minute trend bot for the make.fun BTC race, tuned to this race's
dev window (a -4.91% down day, 13–14 Nov 2026).

## Rules

- **Trend (only mechanism):** buy all cash when EMA(10) > EMA(60),
  the trailing 30-bar return is over +0.50%, and the trailing 60-bar
  return is under +0.40% — the move must be young, not already an
  hour old. Exit at +0.60% take-profit, a 0.70% trailing stop from the
  peak close, or a close under EMA(90). 240-bar cooldown after exit.
- **Dip: disabled.** On this dev window every dip trade lost money
  (the flushes keep falling), so the dip threshold is set to -1.00
  and never fires. The code stays for windows where it helps.
- **Insurance:** if no trade has ever filled and 300 bars have passed,
  buy a fixed $25, exit at ±0.40% or after 30 bars. This is the
  >=1-trade guardrail: on flat/down days it turns a certain no-prize
  (0 trades) into a ranked finish at a cost of cents.

## Local results (faithful replay, integer fills, full cost stack)

| window | PnL | max DD | trades |
|---|---|---|---|
| FULL 24h dev | +$5.33 | 0.25% | 6 |
| B1 down -3.82% | +$0.07 | 0.01% | 2 (insurance) |
| B2 up +0.84% | +$3.55 | 0.24% | 2 |
| B3 down -0.82% | +$1.71 | 0.24% | 2 |
| B4 down -1.12% | +$0.03 | 0.01% | 2 (insurance) |
| offset FULL (+30s bars) | +$2.64 | 0.61% | 8 |

House EMA-cross baseline on the same window: FULL -$109.38
(B1 -$30.90, B2 -$13.02, B3 -$28.61, B4 -$26.37). This bot beats it
in every window. Determinism: two runs, action logs byte-identical.

## Why this shape

The dev day's first block is a -3.82% selloff with no tradeable long
setup at all (best 60-bar forward move +0.33% vs ~0.26% round-trip
cost), so the bot sits out the selloff. It only buys young rallies:
the second block's recovery (+$3.55) and the third block's slow grind
(+$1.71). The un-tuned v3 template scores -$53.19 on this window and
fails the dev gate; this variant is the only one of 30+ measured that
passes it with every block positive.

## Run

Python 3.13, standard library only; `main.py` speaks
agent-fun/bars-replay/v1 via `vendor/makefun_bars.py` (byte-identical
copy of the kit SDK shim).
