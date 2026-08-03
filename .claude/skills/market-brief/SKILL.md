---
name: market-brief
description: Use when someone asks for market news, stock updates, ticker news, a portfolio check, or a watchlist brief.
argument-hint: [ticker | sector | macro | portfolio | watchlist | add <TICKER> <SHARES> @<PRICE> | remove <TICKER> | update <TICKER> <SHARES> [@<PRICE>] | watch <TICKER> [target <PRICE>] | unwatch <TICKER> | import <image path>]
allowed-tools: Read, Write, WebSearch, WebFetch
disable-model-invocation: true
---

For the complete reference on search strategy, sourcing, output format details, and customization, see [reference.md](reference.md).

## What This Skill Does

Tracks a personal stock portfolio and watchlist, then produces a market brief combining your positions' performance with relevant news — for your holdings, your watchlist, and the broader market. Supports quick management commands to add, update, or remove holdings and watchlist entries, plus importing holdings straight from a brokerage app screenshot.

Portfolio and watchlist are saved to the `finance/` folder so they persist across sessions. **`finance/` is gitignored — never commit or push this data.**

⚠️ Price data comes from web search, not a live brokerage or market data feed. Treat all prices and gain/loss figures as approximate and directional, not precise enough to trade on.

## Modes

The first token of `$ARGUMENTS` selects a mode. If it doesn't match a command keyword below, the entire argument is treated as a focus topic (ticker, company, or sector).

| Argument | Mode |
|----------|------|
| _(empty)_ | Full brief — portfolio snapshot + watchlist news + macro roundup |
| `portfolio` | Portfolio-only performance snapshot, no wider market news |
| `watchlist` | Watchlist-only news scan, no portfolio |
| `macro` | Indices, Fed policy, rates, inflation — skip individual names |
| `<ticker/company/sector>` | Focused brief on that topic, cross-referencing whether it's held or watched |
| `add <TICKER> <SHARES> @<PRICE>` | Add a holding, or average into an existing one |
| `remove <TICKER>` | Remove a holding from the portfolio |
| `update <TICKER> <SHARES> [@<PRICE>]` | Replace share count (and optionally cost basis) for an existing holding |
| `watch <TICKER> [target <PRICE>]` | Add a ticker to the watchlist, with an optional target price |
| `unwatch <TICKER>` | Remove a ticker from the watchlist |
| `import <image path>` | Parse a brokerage screenshot, preview extracted holdings/watchlist entries, and save after confirmation |

---

## Mode 1: Full Brief _(default, no argument)_

1. Read `finance/portfolio.md` and `finance/watchlist.md`. If neither exists, explain that no positions or watchlist entries are set up yet, and offer `add`/`watch`/`import` to get started — don't fabricate a brief with no data.

2. For each portfolio holding, search for current price and any notable news from the past 24–48 hours (earnings, guidance changes, analyst moves, M&A, regulatory action). Compute value (`shares × price`), gain/loss vs. cost basis in dollars and percent, and today's change if available.

3. For each watchlist ticker, search for the same categories of news — lighter treatment than holdings since there's no position at stake, but flag anything that would change whether it's worth buying (approaching target price, major catalyst).

4. Search for major index levels (S&P 500, Nasdaq, Dow) and any macro catalysts from the past 24 hours (Fed statements, CPI/jobs data, major geopolitical events affecting markets).

5. Only give a full story write-up (deep-dive treatment) to holdings or watchlist tickers with genuinely notable news — a stock with no news beyond routine price movement gets a single summary line, not a forced story section. Never pad with weak stories to fill space.

6. Compose the brief using the template below and display it.

**Full Brief template:**

```markdown
# 📈 Market Brief — [Month Day, Year]

## 💼 Portfolio Snapshot
| Ticker | Shares | Cost Basis | Price | Value | Gain/Loss | Day Change |
|--------|--------|------------|-------|-------|-----------|------------|
| [TICK] | [N]    | $[X.XX]    | $[X.XX] | $[X,XXX] | [+/-]$[X] ([+/-]X.X%) | [+/-]X.X% |

**Total value:** $[X,XXX.XX] | **Total gain/loss:** [+/-]$[X,XXX] ([+/-]X.X%)

⚠️ Prices are approximate, sourced from web search — not a live feed. Confirm before trading.

---

[2–3 sentence narrative frame tying together what moved your portfolio, your watchlist, and the broader market today.]

---

## [Story headline for a holding or watchlist ticker with notable news]

> [1–2 sentence core fact]

[Deep-dive paragraph 1: facts, numbers, quotes]

[Deep-dive paragraph 2: context, background, competitive/macro framing]

**Why it matters (for you):** [How this affects your position or watch decision specifically — not generic market commentary.]

🔗 [Source Name](url)

---

## 👀 Watchlist Highlights
- **[TICKER]** — [one-line note, e.g. "down 4% this week, now 6% above your $[target] target"]

---

### 🔍 The Big Picture
[2 short paragraphs: the thread connecting today's moves, and what to watch next — earnings dates, Fed meetings, catalysts relevant to your specific holdings.]
```

---

## Mode 2: Portfolio Only (`portfolio`)

1. Read `finance/portfolio.md`. If it doesn't exist, say so and offer `add` or `import` to create the first holding.
2. Fetch current prices for each holding only — skip watchlist and macro searches entirely.
3. Display just the Portfolio Snapshot table and total gain/loss from the template above, plus one line per holding with any same-day news headline if one exists (no full deep-dive sections).

---

## Mode 3: Watchlist Only (`watchlist`)

1. Read `finance/watchlist.md`. If it doesn't exist or is empty, say so and offer `watch <TICKER>` to add the first entry.
2. Fetch current price and recent news for each watchlist ticker.
3. Display the 👀 Watchlist Highlights section only, noting current price vs. target price where a target is set.

---

## Mode 4: Macro Only (`macro`)

1. Search for major index levels and macro catalysts from the past 24–48 hours — Fed policy, rate decisions, inflation/jobs data, major geopolitical market events.
2. Skip individual portfolio/watchlist tickers entirely, even if the files exist.
3. Present as a narrative frame + 2–3 short story sections + Big Picture, same story format as the Full Brief but macro-only.

---

## Mode 5: Focused Topic (`<ticker/company/sector>`)

Triggered when the first argument token doesn't match `portfolio`, `watchlist`, `macro`, `add`, `remove`, `update`, `watch`, `unwatch`, or `import`.

1. Treat the full argument string as the focus (ticker, company name, or sector).
2. Check whether it appears in `finance/portfolio.md` or `finance/watchlist.md` — if so, note your position/watch status at the top of the brief.
3. Search for news on that topic from the past 24–48 hours, falling back to the past week if thin.
4. Produce 2–4 story sections using the same format as the Full Brief, plus a short "Why it matters (for you)" tie-back if it's a held or watched ticker.

---

## Mode 6: Add Holding (`add <TICKER> <SHARES> @<PRICE>`)

1. Parse the ticker (uppercase), share count, and price (after `@`). If any piece is missing or unparseable, ask the user to clarify rather than guessing.
2. If `finance/` doesn't exist, create it. If `finance/portfolio.md` doesn't exist, create it from the template.
3. If the ticker already exists in the portfolio, average the cost basis: `new_cost = (old_shares × old_cost + new_shares × new_price) / (old_shares + new_shares)`, and sum the share counts. Otherwise append a new row.
4. Save with an updated `Last updated` timestamp and confirm: "Added [N] shares of [TICKER] @ $[price]. New position: [total shares] shares @ $[avg cost] avg."

---

## Mode 7: Remove Holding (`remove <TICKER>`)

1. Read `finance/portfolio.md`. If the ticker isn't found, say so.
2. Remove the row entirely (don't just zero it out — unlike day-plan's tasks, there's no history value in keeping a closed position marked in this file).
3. Save and confirm: "Removed [TICKER] from portfolio."

---

## Mode 8: Update Holding (`update <TICKER> <SHARES> [@<PRICE>]`)

1. Read `finance/portfolio.md`. If the ticker isn't found, suggest `add` instead.
2. Replace the share count with the given value. If `@<PRICE>` is also given, replace the cost basis directly (this is a correction, not an average — use `add` if the intent is to average in a new purchase).
3. Save with an updated timestamp and confirm the new values.

---

## Mode 9: Watch (`watch <TICKER> [target <PRICE>]`)

1. Parse the ticker and optional target price (after the `target` keyword).
2. If `finance/` doesn't exist, create it. If `finance/watchlist.md` doesn't exist, create it from the template.
3. If the ticker is already on the watchlist, update its target price if a new one is given; otherwise append a new row with today's date.
4. Save and confirm: "Added [TICKER] to watchlist" (+ " with target $[price]" if given).

---

## Mode 10: Unwatch (`unwatch <TICKER>`)

1. Read `finance/watchlist.md`. If the ticker isn't found, say so.
2. Remove the row.
3. Save and confirm: "Removed [TICKER] from watchlist."

---

## Mode 11: Import from Screenshot (`import <image path>`)

1. Read the image at the given path using the Read tool.
2. Extract only fields that are explicitly visible and labeled — ticker, shares, average cost/cost basis, current price, market value. Never derive a field that isn't shown (e.g., don't compute "cost basis" from value ÷ shares unless the screenshot itself labels a per-share cost).
3. Classify the screenshot: if share counts and/or cost basis are visible, default to **portfolio**; if it's a plain ticker list with no position size (a typical brokerage "Watchlist" view), default to **watchlist**. State the classification and ask the user to confirm or override it.
4. Present a preview table of everything extracted — ticker, shares, cost basis, or whatever fields apply — before writing anything. Do not save to any file until the user confirms the preview is accurate.
5. If any field is missing, cut off, or ambiguous, flag it explicitly in the preview and ask the user to supply it — never guess or estimate a financial figure.
6. Once confirmed, apply the same merge logic as Mode 6 (portfolio: average cost basis into existing tickers, append new ones) or Mode 9 (watchlist: append new tickers, update target if given).
7. Save and confirm what was added. Do not save or reference the screenshot file itself anywhere in `finance/` — only the confirmed structured data is persisted.

---

## File Templates

**`finance/portfolio.md`:**

```markdown
# 💼 Portfolio

| Ticker | Shares | Cost Basis | Purchase Date | Notes |
|--------|--------|------------|----------------|-------|
| [TICK] | [N]    | $[X.XX]    | [YYYY-MM-DD]   |       |

_Updated: [YYYY-MM-DD]_
```

**`finance/watchlist.md`:**

```markdown
# 👀 Watchlist

| Ticker | Added | Target Price | Notes |
|--------|-------|---------------|-------|
| [TICK] | [YYYY-MM-DD] | $[X.XX] |       |

_Updated: [YYYY-MM-DD]_
```

## Notes

- `finance/` is gitignored at the repo root — never remove that entry, and never suggest committing portfolio or watchlist data.
- Always read the existing portfolio/watchlist files before reporting on them — never fabricate positions or prices.
- Price and gain/loss figures are search-derived estimates. Always include the "not a live feed" disclosure in any output that shows a computed price or gain/loss.
- Never pad a brief with a full story section for a holding that has no notable news — a summary line is enough.
- If a ticker is ambiguous (e.g., a common word), disambiguate with the exchange or full company name in the search query.
- Import (Mode 11) never writes without explicit user confirmation of the parsed preview — a misread digit in a screenshot could otherwise silently corrupt cost basis records.
