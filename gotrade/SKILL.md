---
name: gotrade
description: >
  Personal US stock research skill for pre-trade checks in Gotrade. Trigger this
  skill when the user types /gotrade, asks about buying, selling, holding, adding
  to, trimming, or exiting a US stock position, mentions a ticker or company name
  alongside price movement, or wants a quick research summary before making a
  trade. For casual market discussion, use this skill when there is any clear
  trading-decision intent. It gathers current web research, source-backed news,
  analyst context, fundamentals, price context, personal risk reminders, and a
  visual Buy/Hold/Sell research signal mix.
---

# Gotrade — Personal Stock Research Skill

A research assistant for long-term investors who want a quick but structured
analysis before buying, selling, holding, adding to, or trimming a US stock
position in Gotrade. It does not execute trades or give personalized financial
advice. It gathers and synthesizes publicly available information so the user
can make a more informed decision.

---

## How to Trigger

The user can invoke this skill in any of these ways:

```
/gotrade AAPL
/gotrade Tesla
/gotrade NVDA - it's down 10% this week, is it worth buying?
Should I buy Google stock? It dropped a lot this month.
Thinking of selling some MSFT. What should I check first?
```

Extract the ticker or company name from the input. If the ticker is ambiguous,
resolve it with a quick search and state the assumed company before proceeding.

---

## Research Rules

Gather evidence for all four research areas before writing the final analysis.
Use the current date in search queries instead of hard-coded months or years.
Prefer recent sources and show the user when information is dated, delayed, or
uncertain.

Use a mix of source types where available:
- Company investor relations, earnings releases, SEC filings, or earnings call
  transcripts
- Reputable market data pages for price, valuation, and 52-week range
- Reputable financial news sources for recent developments
- Analyst-rating aggregators or broker research summaries for consensus context

Do not rely only on search-result snippets or promotional/SEO pages. If search
results are unclear or unavailable for any step, say so clearly rather than
guessing.

---

## Research Process

### Step 1 - Recent News (last 2-4 weeks)
Search for current news using queries like:

`[TICKER] stock news [current month] [current year]`

Look for:
- Earnings results, guidance, or material financial updates
- Product launches, acquisitions, lawsuits, or operational changes
- Leadership changes such as CEO, CFO, or board announcements
- Regulatory, legal, or government issues
- Macro or sector factors affecting the stock

### Step 2 - Analyst Sentiment
Search for current analyst context using queries like:

`[TICKER] analyst rating buy hold sell price target [current year]`

Look for:
- Current consensus, if available
- Recent upgrades or downgrades
- Price target changes and the reason for the change
- Dispersion between bullish and bearish analyst views

### Step 3 - Fundamentals
Search for current fundamentals using queries like:

`[TICKER] P/E ratio revenue earnings growth cash flow debt [current year]`

Look for:
- Valuation context such as P/E, forward P/E, or price/sales where relevant
- Revenue trend: growing, flat, or shrinking
- Profitability, margins, free cash flow, and earnings quality
- Debt, dilution, cash burn, or balance-sheet concerns

### Step 4 - Price Context
Search for current price context using queries like:

`[TICKER] stock 52-week high low current price [current year]`

Look for:
- Current price and whether market data may be delayed
- 1W / 1M / 3M movement if available
- Position within the 52-week range
- Whether the move looks like normal volatility or a material break in trend

---

## Personal Risk Check

Before the signal mix, include a short risk checkpoint. If the user did not
provide this context, do not stop the analysis; instead, list the unknowns the
user should consider before trading:

- Time horizon: short-term trade or long-term investment?
- Position status: new position, adding, holding, trimming, or exiting?
- Position size: would the trade create concentration risk?
- Diversification: is the user already exposed to the same sector, theme, or
  mega-cap factor?
- Risk tolerance: can the user tolerate further downside without panic-selling?
- Liquidity/currency: Gotrade prices are in USD; consider FX impact, fees, and
  local tax implications where relevant.

---

## Output Format

Always produce output in this structure. Keep the wording clear that the result
is a research signal mix, not a command to trade.

---

### [COMPANY NAME] ([TICKER]) - Gotrade Research Check

**Price Context**
- Current price or quoted range, source/date, and whether the data may be delayed
- Recent movement (1W / 1M / 3M if available)
- One or two sentences explaining the likely drivers of the move

---

**Recent News**
List 3-5 bullet points. Each bullet should be one clear sentence, followed by a
tag: `[Positive]`, `[Negative]`, or `[Neutral]`

Example:
- Apple beat quarterly earnings estimates, driven by services growth. `[Positive]`
- iPhone sales in China declined year-over-year. `[Negative]`

---

**Fundamentals Snapshot**

| Factor | Status | Notes |
|---|---|---|
| Valuation | Cheap / Fair / Expensive / Unclear | Brief reason |
| Revenue trend | Growing / Flat / Declining / Unclear | Brief reason |
| Profitability | Healthy / Weak / Concern / Unclear | Brief reason |
| Balance sheet / cash flow | Healthy / Watch / Concern / Unclear | Brief reason |
| Red flags | None found / Yes - [describe] / Unclear | Brief reason |

---

**Analyst Context**
- Overall consensus: **[Bullish / Mixed / Bearish / Unclear]**
- Recent changes: [upgrades, downgrades, price target changes, or "none found"]

---

**Personal Risk Check**
- Time horizon: [known or not provided]
- Position context: [known or not provided]
- Concentration/diversification note: [brief reminder]
- Practical reminder: check Gotrade for live price, available cash, FX, fees, and
  any tax implications before placing an order.

---

**Research Signal Mix**

Buy case:
- [Reason 1]
- [Reason 2]
- [Reason 3]

Hold / wait case:
- [Reason 1]
- [Reason 2]
- [Reason 3]

Sell / avoid case:
- [Risk 1]
- [Risk 2]
- [Risk 3]

---

**Signal Breakdown**

After completing the analysis, calculate a Buy / Hold / Sell research signal
split using the scoring guide below, then generate a React artifact pie chart.

---

## Scoring Guide

Use the research findings to assign a Buy / Hold / Sell signal percentage
split. The three numbers must always add up to 100%.

| Signal | Buy % | Hold % | Sell % |
|---|---|---|---|
| Strong positive setup - solid fundamentals, positive news, analysts bullish, valuation reasonable or attractive | 70 | 20 | 10 |
| Moderate positive setup - mixed news but fundamentals intact, dip looks like normal volatility | 50 | 30 | 20 |
| Neutral setup - unclear picture, mixed signals, fair valuation, or no clear catalyst | 20 | 60 | 20 |
| Caution setup - weakening fundamentals, negative news, or significantly stretched valuation | 10 | 30 | 60 |
| Strong avoid setup - serious red flags, business deterioration, or analysts turning sharply bearish | 5 | 15 | 80 |

Use judgment for cases in between. The split is a synthesis of all research
steps and should reflect uncertainty. It is not a personalized recommendation
or prediction.

---

## Pie Chart Artifact

After the text analysis, generate a React artifact with a pie chart showing the
Buy / Hold / Sell research signal mix.

Use these exact colors:
- Buy: `#22c55e` (green)
- Hold: `#eab308` (yellow)
- Sell: `#ef4444` (red)

The chart should include:
- The company name and ticker as the title
- Each slice labeled with the category name and percentage
- A clean legend below the chart
- A brief one-line research summary below the legend

Example summary line: "Moderate positive setup - fundamentals look intact, but
recent weakness and valuation deserve caution."

Keep the design clean and minimal. No clutter.

---

## Disclaimer

Always end every analysis with this line, exactly as written:

> This is research assistance only, not financial advice. Always do your
> own research and make your own informed decisions before buying or selling
> any stock.

---

## What This Skill Does NOT Do

- It does not execute trades
- It does not predict future prices
- It does not give a definitive "you should buy" or "you should sell"
- It does not provide personalized financial, tax, or legal advice
- It does not replace checking live prices, order details, fees, FX, or available
  cash inside Gotrade before placing an order
- It should not be used for day trading, options, leverage, margin, or short-term
  speculation without explicit extra risk warnings

---

## Example Invocations

**User:** `/gotrade NVDA`
**User:** `AAPL is down 15% this month, should I buy more?`
**User:** `What do you think about Tesla right now? Thinking of selling.`
**User:** `/gotrade Microsoft - been watching it for a while`

All of the above should trigger this skill.
