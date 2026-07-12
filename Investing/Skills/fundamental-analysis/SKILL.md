---
name: fundamental-analysis
description: >-
  Perform a complete fundamental analysis of a publicly traded company and
  deliver a 0-100 "worth owning long term" score with a beginner-friendly
  report. Use this skill whenever the user gives a stock ticker or company
  name and wants to know anything about whether it's a good investment,
  whether to buy/hold/sell it, how healthy the business is, or asks for
  "fundamentals", "analysis", "research", a "profile", or a "score" on a
  stock. Trigger even for casual phrasings like "what do you think of AAPL",
  "is Costco a buy", "should I own this long term", or just a bare ticker
  symbol. Do NOT use for pure chart/technical-analysis requests, crypto,
  options strategies, or portfolio allocation questions.
---

# Fundamental Analysis: Long-Term Ownership Score

Analyze a company's fundamentals and produce a 0–100 score answering one
question: **is this business worth owning for the long term (5+ years)?**
The lens is "quality at a fair price" — a wonderful business at a reasonable
valuation beats a cheap mediocre one.

The user is a beginner at fundamental analysis. Every metric in the report
gets a one-line plain-English explanation. Never assume they know what ROIC
or free cash flow means — teach as you go, without being condescending.

## Workflow

### Step 1 — Identify the company

Resolve the ticker/name to a specific company and exchange. If ambiguous
(e.g., "Apple" vs. Apple Hospitality REIT), pick the obvious large-cap match
and note the assumption. If it's an ETF, fund, or crypto, tell the user this
skill scores individual operating companies and offer a general overview
instead.

### Step 2 — Gather data (search, don't guess)

Financial figures change every quarter and prices change daily. **Never
fill in numbers from memory** — a fabricated P/E ratio poisons the whole
score. Use web search and page fetches to collect real, current data.

Good sources (try in this order; cross-check anything surprising):
- `stockanalysis.com/stocks/<ticker>/financials/` — clean 5-year statements
  (also `/balance-sheet/`, `/cash-flow-statement/`, `/ratios/`)
- `macrotrends.net` — long-term margin/ROE/debt history
- Company investor-relations pages and latest 10-K/annual report — for
  segment detail, risks, and management commentary
- Recent news search — for anything material in the last 6 months
  (lawsuits, guidance cuts, CEO changes, big acquisitions)

Collect, at minimum:
- Current price, market cap, and the date of the data
- 5 fiscal years + TTM of: revenue, gross/operating/net income, EPS
  (diluted), free cash flow, shares outstanding
- Balance sheet: total debt, cash, equity, interest expense
- Ratios: ROIC or ROE, current ratio, net debt/EBITDA
- Valuation: P/E (trailing and forward), P/FCF, EV/EBITDA, PEG, and the
  company's own 5-year average P/E for comparison
- Dividend info if any: yield, payout ratio, growth streak
- Qualitative: what the company actually sells, who competitors are,
  analyst consensus and price targets (context only, not the score)

If a stock-data MCP connector is available in the session, prefer it over
scraping. If a figure truly can't be found, say so in the report and score
that item conservatively — never invent it.

### Step 3 — Score with the rubric

Read `references/scoring-rubric.md` and score all six pillars exactly as
specified there:

| Pillar | Points |
|---|---|
| 1. Profitability & business quality | 25 |
| 2. Growth | 20 |
| 3. Financial health (balance sheet) | 15 |
| 4. Competitive moat | 15 |
| 5. Valuation | 15 |
| 6. Management & shareholder friendliness | 10 |
| **Total** | **100** |

Then apply the red-flag overrides in the rubric (they cap the maximum
score — a great-looking scorecard with a going-concern warning is not an
85). Show your work: the report must list points earned per pillar so the
user can see *why* the score is what it is, and learn the framework over
time.

### Step 4 — Write the report

Read `references/report-template.md` and follow it. Key requirements:

- Open with the verdict box: score, band label, and a 2–3 sentence
  bottom line in plain English.
- Every metric line pairs the number with a one-line "what this means"
  explanation and a ✅ / ⚠️ / ❌ signal.
- Include a "What would change my mind" section — the 2–3 things that
  would raise or lower the score materially. This teaches the user what
  actually drives the thesis.
- End with the standard disclaimer from the template. This is educational
  analysis, not financial advice — always include it, never soften it away.

Score bands:

| Score | Band | Meaning |
|---|---|---|
| 80–100 | Strong long-term candidate | High-quality business at a reasonable price |
| 65–79 | Good, with caveats | Own-able; watch the listed weak spots |
| 50–64 | Watchlist | Decent business but something's off (often price) |
| 35–49 | Weak | Meaningful problems; needs a turnaround thesis |
| 0–34 | Avoid | Broken fundamentals or serious red flags |

### Step 5 — Save and deliver

1. Show the full report in chat.
2. Save it to the user's connected folder as
   `Analysis Reports/<TICKER> <YYYY-MM-DD> (score <N>).md` — the filename
   itself becomes a browsable library over time. Create the
   `Analysis Reports` folder if it doesn't exist.
3. Present the saved file to the user.

If the user asks to compare multiple tickers, run the full workflow per
ticker, then add a short comparison table (score per pillar, side by side)
and a one-paragraph "if I had to pick one" summary.

## Beginner-teaching notes

- Explanations belong inline next to the numbers, not in a glossary dump.
- Follow the three-beat rhythm everywhere: the number → what the metric
  means in plain English → why it matters for *this specific company*.
  "P/E is 46x" teaches nothing; "46x vs. its own 10-year median of 38x —
  you're pre-paying for years of growth" teaches the framework.
- Always include the "key insight" section: the one mechanism that makes
  this particular business work (e.g., Costco's membership-fee flywheel).
  Metrics describe the machine; this section explains it.
- Prefer analogies to jargon: "ROIC is like the interest rate the business
  earns on the money it reinvests in itself."
- When a metric is bad, say what it means practically ("debt is 4x annual
  cash earnings — a bad year or two could force dividend cuts"), not just
  that it's bad.
- `references/metrics-guide.md` has ready-made plain-English explanations
  and healthy/unhealthy thresholds for every metric — use it so the
  explanations stay consistent across reports.

## Integrity rules

- Date-stamp the data ("prices and financials as of <date>").
- Never fabricate a number. Missing data → say so, score conservatively.
- The score is an opinionated synthesis, not a prediction. Don't present
  it with false precision ("73" not "73.4").
- Don't let one exciting narrative override the rubric. If the numbers and
  the story disagree, say so explicitly — that tension is the most useful
  thing you can show the user.
