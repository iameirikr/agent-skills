---
name: fundamental-screener
description: >-
  Batch-score a LIST of stock tickers on fundamentals and return a single
  ranked table (score out of 100 + verdict band + one-line takeaway per
  ticker) instead of full reports. Use this skill whenever the user provides
  two or more tickers at once and wants them screened, scored, ranked,
  compared as a batch, or filtered — especially watchlists pasted from a
  ThinkorSwim/broker technical scan, phrases like "screen these", "score
  this list", "which of these are worth owning", "run fundamentals on my
  watchlist", or just a pasted block of ticker symbols. Do NOT use for a
  single ticker or when the user wants a detailed report on each company —
  that's the fundamental-analysis skill.
---

# Fundamental Screener: Batch Scores for a Watchlist

The user runs technical scans (ThinkorSwim — Bollinger Bands, RSI, etc.)
and brings the resulting watchlist here. The job: score every ticker's
fundamentals on the same 0–100 scale as the full fundamental-analysis
skill, and return one ranked table they can filter in seconds. They use
the survivors to hunt options trades — speed and comparability matter,
1,500-word essays do not.

## Workflow

### Step 1 — Parse the list

Extract ticker symbols from whatever was pasted: comma/space/newline
separated, or a full watchlist export with price columns and headers —
grab the symbols, ignore the rest. Echo the parsed list back in one line
("Screening 8 tickers: AAPL, MSFT, ...") so a mangled paste gets caught
early. Skip and note anything that isn't a scoreable operating company
(ETFs, funds, indexes, crypto, warrants).

### Step 2 — Research each ticker (condensed, real data)

Same integrity rule as the full skill: **never fill in numbers from
memory** — search and fetch current data, and date-stamp the batch.
`stockanalysis.com/stocks/<ticker>/` (plus `/financials/`, `/ratios/`)
covers most of it; add one news search per ticker for red flags from the
last 6 months.

Condensed data set per ticker (enough to score, not enough to write an
essay): price/market cap; TTM revenue, operating margin, net income, FCF;
5-yr revenue and EPS trend (direction and rough CAGR, not every line
item); ROIC or ROE; net debt/EBITDA and interest coverage; P/E (and
P/FCF) vs. the company's own 5-yr average; share count trend; a quick
moat judgment; any red-flag news.

If you can run parallel subagents, give each one 2–3 tickers and have
them return the filled-in scoring line per ticker. Otherwise work through
the list sequentially. Either way, every ticker gets scored with the same
rubric — consistency across the table is the whole point.

### Step 3 — Score with the condensed rubric

Read `references/quick-rubric.md` and score all six pillars per ticker.
The weights and bands mirror the full fundamental-analysis skill, so a
stock scoring 63 here should land near 63 in a deep dive. Apply the
red-flag caps — they matter *more* in a screen, because a table row can't
carry nuance.

### Step 4 — Deliver the table

Output ONE table, sorted by score, best first:

| # | Ticker | Company | Score | Band | Buy below | Takeaway |
|---|---|---|---|---|---|---|
| 1 | XXXX | Name | 78 | Good | $84 | One sentence: biggest strength vs. biggest concern. |

**Buy-below column**: the price at which the stock would represent good
value per the rubric — computed per the "Buy-below price" section of
`references/quick-rubric.md`. Only for scores ≥50; print "—" for Weak
and Avoid names (a fundamentals-based entry price on a broken business
is false comfort — those are technical trades, not ownership candidates).
If the current price is already at or under the buy-below, mark it
"$X ✓ (at value now)".

Rules for the table:
- The takeaway must state the *decisive* factor, not a summary ("great
  margins but priced at 60x — valuation is the whole question", not
  "solid company with pros and cons").
- Flag any red-flag cap right in the takeaway ("capped at 30: auditor
  resigned in March").
- Below the table, 2–4 sentences max: the date of the data, which names
  cluster where, and anything that surprised you versus the ticker's
  reputation.
- Close by offering a full deep-dive report on any ticker they shortlist
  (the fundamental-analysis skill handles that).

Band legend (same as the full skill): 80–100 Strong candidate ·
65–79 Good, with caveats · 50–64 Watchlist · 35–49 Weak · 0–34 Avoid.

### Step 5 — Save

Save the table plus per-ticker pillar scores (as an appendix — one
compact six-pillar line per ticker so the user can see *why* each score
is what it is) to the connected folder as
`Analysis Reports/Screen <YYYY-MM-DD> (<N> tickers).md`. Present the file.

End the file with the one-line disclaimer: *Educational analysis from
public data as of <date>, not financial advice — verify before trading.*

## Judgment notes

- Scores must be comparable across the table AND with past full reports.
  When torn between two bands, pick the same way the full rubric would.
- A screen is a filter, not a verdict. When data was thin for a ticker
  (micro-caps, recent IPOs), mark the score with a `*` and say what was
  missing — a shaky 55* should read differently than a solid 55.
- Don't inflate scores to be nice. The user is filtering a technical
  scan; a table where everything lands 60–75 is useless to them.
