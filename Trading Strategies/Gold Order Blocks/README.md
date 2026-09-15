# Gold Order Block Strategy (Entries Only)

Prototype v1 — order blocks + BOS/CHoCH structure, for GC1! (or any gold
feed). Entries only, on purpose: no stop loss or take profit yet, so we
can look at the raw signal quality first before adding exits.

## How it works

1. **Structure** — swing highs/lows are found with a 10-bar (each side)
   pivot. When price closes beyond the last swing high/low, that's a
   structure break:
   - **CHoCH** (change of character) if it reverses the prior trend.
   - **BOS** (break of structure) if it continues the prior trend.
   - Both are plotted as labels on the chart so you can sanity-check
     them against what you'd mark by hand.
2. **Order block** — the last opposite-colour candle before the
   impulsive leg that caused the break (last down-close candle before a
   bullish break = bullish OB; last up-close candle before a bearish
   break = bearish OB).
3. **FVG filter** — by default, an order block only counts if the
   impulse leg between it and the breakout contains a 3-candle Fair
   Value Gap (a classic ICT confirmation that the move was genuinely
   imbalanced, not just drifting). Turn off "Require FVG" to see raw
   order blocks without this filter.
4. **Supply & demand confirmation** — independently of order blocks, the
   script also detects supply/demand zones (a small tight-range "base"
   followed by a strong impulsive candle breaking away from it — same
   base+move logic as before, drawn in green/red). By default, an order
   block entry only fires if price is *also* sitting inside an active
   same-direction supply/demand zone at that moment — confluence between
   the two systems, not just one signal on its own. Turn off "Require
   S/D zone overlap" to go back to order-block-only entries.
5. **Entry** — as soon as price wicks back into a fresh (untouched)
   order block (and, if confluence is required, into a matching
   supply/demand zone at the same time), a market entry fires in that
   direction. Opposite signals reverse the position (pyramiding is off,
   one position at a time). No stop/target yet — an opposite-direction
   OB touch is currently the only thing that closes a trade.

## Setup in TradingView

1. Open your gold chart (GC1! or otherwise) — built with 5m in mind but
   the swing length is adjustable for other timeframes.
2. Pine Editor → paste in `gold-order-block-strategy.pine` → Save →
   confirm it's actually added to the chart (not just open in the
   editor).
3. For a clean read while testing, hide every other indicator on the
   chart and leave only this one visible.
4. Watch the BOS/CHoCH labels, the blue/orange order block boxes, and
   the green/red supply/demand boxes first — before even looking at
   Strategy Tester, check that the zones and structure calls match what
   you'd draw by hand.

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
- **S/D base/breakout ATR factors** — same idea as the order block FVG
  filter: loosen `S/D base candle max range` or lower `S/D breakout
  candle min range` if too few supply/demand zones ever form (meaning
  confluence almost never fires); tighten if there are too many
  low-quality zones.
- **Require S/D zone overlap** — the main lever for "how strict is
  confirmation." On means fewer, more selective entries; off means
  order blocks alone decide, same as before this change.

## Known limitations (v1, on purpose)

- No stop loss / take profit — a trade only closes when an opposite
  signal fires. This is deliberate for now so we can judge entries in
  isolation; we'll add proper exits once the entry logic looks good.
- Position size is a fixed contract count (`Contracts per entry`),
  since there's no stop distance yet to risk-size against.
- Order blocks remain "valid" until price closes fully through them —
  they are not time-limited beyond the `Zone box extend` visual setting
  and the `Max order blocks kept per side` cap.
