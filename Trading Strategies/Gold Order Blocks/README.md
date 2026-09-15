# Gold Order Block Strategy (Entries Only)

Prototype v1 — order blocks + BOS/CHoCH structure, for GC1! (or any gold
feed). Entries only, on purpose: no stop loss or take profit yet, so we
can look at the raw signal quality first before adding exits.

## How it works

1. **Structure** — swing highs/lows are found with a 10-bar (each side)
   pivot. When price closes beyond the last swing high/low, that's a
   structure break: **CHoCH** if it reverses the prior trend, **BOS** if
   it continues it. This still drives which order blocks are valid
   (toggle "Trade BOS/CHoCH order blocks" independently), but neither is
   labeled on the chart anymore — only actual BUY/SELL entries are.
2. **Order block** — the last opposite-colour candle before the
   impulsive leg that caused the break (last down-close candle before a
   bullish break = bullish OB, drawn in green with a solid border; last
   up-close candle before a bearish break = bearish OB, drawn in red).
3. **FVG filter** — off by default now (more order blocks show up on
   chart this way). Turn on "Require FVG" if you want the classic ICT
   confirmation that the impulse leg left a 3-candle Fair Value Gap,
   which cuts the number of order blocks down significantly.
4. **Supply & demand zones** — independently of order blocks, the script
   also detects supply/demand zones (a small tight-range "base" followed
   by a strong impulsive candle breaking away from it), drawn in the
   same green/red as order blocks but with a **dashed** border so the
   two box types stay visually distinct even when they overlap.
5. **Rejection candle at the zone** — by default, touching a zone isn't
   enough on its own; that same bar must also print a rejection candle
   (bullish/bearish pin bar or engulfing) in the zone's direction. Turn
   off "Require rejection candle" to go back to immediate-touch entries.
6. **Order block OR supply/demand zone** — by default, *either* an order
   block touch or a supply/demand zone touch (each with its own
   rejection candle) is enough to fire a signal — this is what gives you
   more entries. Turn on "Require BOTH order block AND S/D zone" to
   switch to strict confluence instead (fewer, more selective entries).
7. **Higher-timeframe trend bias** — a 1-hour EMA 20/50 cross (both
   adjustable) sets the "real" trend direction. By default, longs only
   fire when the 1H trend is bullish and shorts only when it's bearish —
   this is the main lever meant to stop counter-trend/chop entries and
   get you into the bigger moves. The chart background tints faint
   green/red so you can see the current HTF bias at a glance (toggle
   "Shade chart background by HTF bias" to turn that off).
8. **Entry** — when a signal passes all of the above, a market entry
   fires and a green "BUY" / red "SELL" tag is dropped on the entry bar.
   Opposite signals reverse the position (pyramiding is off, one
   position at a time). No stop/target yet — an opposite-direction touch
   is currently the only thing that closes a trade.

## Setup in TradingView

1. Open your gold chart (GC1! or otherwise) — built with 5m in mind but
   the swing length is adjustable for other timeframes.
2. Pine Editor → paste in `gold-order-block-strategy.pine` → Save →
   confirm it's actually added to the chart (not just open in the
   editor).
3. For a clean read while testing, hide every other indicator on the
   chart and leave only this one visible.
4. Watch the order block boxes (solid border), the supply/demand boxes
   (dashed border), the background tint (HTF bias), and the BUY/SELL
   tags — before even looking at Strategy Tester, check that entries
   only fire with the background tint and that they're catching moves
   that actually run, not just noise.

## What to tune together from here

- **Swing length** — currently 10 bars each side. Tighter catches more
  structure shifts (and more noise); wider is slower but higher
  conviction.
- **Require FVG** — toggle this to compare order-block quality with and
  without the imbalance filter.
- **BOS vs CHoCH** — toggle each independently to see which type of
  order block is actually producing good entries on your chart.
- **Max bars to search for order block** — how far back we're allowed
  to look for the "last opposite candle" before the impulse. If order
  blocks look too far from the breakout, lower this.
- **S/D base/breakout ATR factors** — loosen `S/D base candle max range`
  or lower `S/D breakout candle min range` if too few supply/demand
  zones ever form; tighten if there are too many low-quality zones.
- **Require BOTH order block AND S/D zone** — off (either counts) gives
  more entries; on (strict confluence) gives fewer, higher-conviction
  ones. This is the main "quantity vs. quality" lever now.
- **Require rejection candle** / **Min wick-to-body ratio** — how
  strict the candle confirmation at a zone touch has to be. Loosen the
  ratio (lower than 1.0) if valid-looking rejections are getting
  rejected by the filter.
- **HTF timeframe / EMA lengths** — 1H with EMA 20/50 is the starting
  point. Try 15m for a faster-reacting bias, or 4H for a slower, more
  stable one, and see which keeps you out of the chop without missing
  the big moves.

## Known limitations (v1, on purpose)

- No stop loss / take profit — a trade only closes when an opposite
  signal fires. This is deliberate for now so we can judge entries in
  isolation; we'll add proper exits once the entry logic looks good.
- Position size is a fixed contract count (`Contracts per entry`),
  since there's no stop distance yet to risk-size against.
- Order blocks remain "valid" until price closes fully through them —
  they are not time-limited beyond the `Zone box extend` visual setting
  and the `Max order blocks kept per side` cap.
