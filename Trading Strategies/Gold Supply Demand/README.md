# Gold Supply & Demand Strategy (5m)

A TradingView Pine Script v5 strategy for XAUUSD on the 5-minute chart,
based on a "base + strong move" supply/demand method.

## Important caveat

The two source videos couldn't be watched directly (YouTube is blocked
in this environment, and the transcripts weren't provided), so this was
built from a description of the approach rather than the exact rules
taught in the videos. Treat it as a solid, standard implementation of
supply/demand trading — compare it against the videos and adjust the
inputs (or the code) if anything doesn't match what's taught.

## How it works

1. **Zone detection** — looks for 1-3 tight-range "base" candles (range
   below a multiple of ATR) immediately followed by a strong impulsive
   candle that breaks away from them. The base candles' high/low become
   the zone:
   - Bullish break away from the base → **demand zone**.
   - Bearish break away from the base → **supply zone**.
2. **Entry** — when price later wicks back into an untouched zone and
   prints a rejection candle (pin bar or engulfing) in the zone's
   direction, an entry triggers on the next bar's open.
3. **Stop loss** — placed just beyond the far edge of the zone, plus a
   small buffer (`Stop buffer beyond zone` input, default $0.50).
4. **Take profit** — targets the nearest opposite zone (nearest supply
   zone above for longs, nearest demand zone below for shorts). If no
   opposite zone exists yet, it falls back to a fixed risk:reward
   multiple (`Fallback R:R`, default 2.0).

Only one position is open at a time, and by default only "fresh"
(never-touched) zones are tradeable — the highest-probability setup in
most supply/demand teaching.

## Setup in TradingView

1. Open XAUUSD (or your broker's gold symbol) on the **5-minute** chart.
2. Pine Editor → New blank script → paste the contents of
   `gold-supply-demand-strategy.pine` → Save → Add to chart.
3. Open **Strategy Tester** to see backtest results, or set alerts on
   the "Long Entry" / "Short Entry" alert conditions to trade manually.

## Inputs worth tuning

- **Base candles / ATR factors** — controls how tight a base and how
  strong a breakout must be to qualify as a zone. Loosen
  `smallRangeFactor` or lower `breakoutFactor` if too few zones form on
  your feed; tighten them if too many low-quality zones appear.
- **Only trade fresh zones** — turn off to allow re-entries on zones
  that have already been tested once.
- **Risk per trade (%)** — position size is computed from account
  equity, the stop distance, and the symbol's own point value
  (`syminfo.pointvalue`), so it sizes correctly whether you're on a
  futures contract (e.g. GC1!, $100/point) or a spot/CFD feed (e.g.
  OANDA:XAUUSD, $1/point) — no manual adjustment needed. If you'd
  rather not risk-size at all, turn on **Use fixed quantity** and set a
  fixed lot/contract size instead.
- **Session filter** — off by default; enable it to restrict entries to
  a specific session window (e.g. London/New York) if that's part of
  what the videos taught.

## Known limitations

- Zone detection and rejection logic are simplified proxies for
  discretionary supply/demand reading — they won't catch every zone a
  human trader would draw, and may catch some a human wouldn't.
- Backtest results depend heavily on the ATR/base-length settings and
  your broker's historical data quality on 5m gold.
- This has not been forward-tested; validate it on a demo account
  before trading it live.
