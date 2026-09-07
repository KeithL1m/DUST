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

⚠️ Price data comes from live quote pages, cross-checked across two independent sources — not a live brokerage feed. Treat gain/loss figures as accurate to within a few minutes of market movement, not tick-by-tick.

### Price Verification (Two-Source Cross-Check)

A single fetched source can be silently wrong — a page can be mid-outage and serve a stale snapshot with a normal-looking timestamp (this has happened with Yahoo Finance), or different aggregators can disagree by double digits on a volatile stock. Never trust one source, and never use WebSearch's aggregated snippets for a price — they summarize multiple stale/cached pages and are the least reliable option. For every ticker (holdings and watchlist), fetch the price directly with WebFetch from two independent quote pages:

1. **Source A:** `https://www.google.com/finance/quote/<TICKER>:<EXCHANGE>` (e.g., `NASDAQ`, `NYSE`, or `TSE` for Canadian tickers)
2. **Source B:** `https://stockanalysis.com/stocks/<ticker>/` (lowercase ticker; use `https://stockanalysis.com/quote/tsx/<TICKER>/` for TSX-listed tickers)

Then:
1. If either page explicitly says its data is delayed, stuck, or experiencing an outage (e.g., a "temporary issues" or "data is currently delayed" banner), discard that source for this ticker and pull a third as a tiebreaker — Yahoo Finance (`finance.yahoo.com/quote/<TICKER>/`) or CNBC (`cnbc.com/quotes/<TICKER>`).
2. If the two remaining sources agree within ~0.5%, use the one with the more recent explicit timestamp (or their average if timestamps match).
3. If they disagree by more than ~0.5%, don't silently pick one — surface it in the output (e.g., `⚠️ sources disagree: $X vs $Y`) rather than presenting false precision, and lean toward whichever has the more recent, explicit timestamp.
4. Note the retrieval time once near the top of the brief (e.g., "Prices as of 2:14 PM EDT") so staleness is visible at a glance without repeating it per row.

This doubles the fetch cost per ticker, but it's the only way to catch a single-source outage before it silently corrupts an entire brief.

### Rising/Falling Indicators

Plain markdown can't render actual font color, so every gain/loss and day-change figure gets a colored-dot prefix instead: 🟢 for positive/rising, 🔴 for negative/falling, ⚪ for flat/unchanged (roughly ±0.05% or explicitly reported as unchanged). Apply this to every dollar and percent figure that represents a move — portfolio gain/loss, day change, and any price move mentioned in a story section or watchlist bullet. Example: `🔴 -$1.96 (-0.16%)` or `🟢 +0.41%`.

### Investment Opinion ("🎯 My Take")

Every brief that shows priced positions — Full Brief, Portfolio Only, Watchlist Only, and Focused Topic — includes a short, explicitly-labeled opinion per name: 🟢 **Add** / 🟡 **Hold** / 🔴 **Trim** for holdings, or 🟢 **Buy** / 🟡 **Wait** / 🔴 **Pass** for watchlist names. This is opinion, not fact — treat it with the same rigor as the rest of the brief, but never dress it up as more certain than it is.

**Two required inputs, from two separate searches — don't rely on one to surface the other:**
1. **Market news** (from the news search already done in Mode 1 steps 2–3): momentum and its actual cause, upcoming catalysts, M&A/regulatory events, explicit valuation commentary from a source.
2. **Expert/analyst opinion** (a dedicated search, run for every holding and every watchlist name getting a My Take line — see "Analyst Research Search" below): current Wall Street consensus rating (Buy/Hold/Sell), average price target and how far it sits from the current price, and any *named-firm* rating change in the last 1–2 weeks (e.g., "Morgan Stanley upgraded to Overweight," not a vague "analysts are bullish").

**Synthesize, don't just concatenate.** State the news signal and the analyst signal, then say plainly whether they agree or pull in different directions:
- Agreement reinforces the call: `**MSFT** — 🟢 Add — Azure's 43% growth print backs up the Street's own optimism: 56 of 58 analysts rate it Buy/Strong Buy with an average target of $563, well above the current price.`
- Disagreement is itself the most useful thing to flag, not something to paper over: `**FIG** — 🟡 Hold — Today's news is the BofA Buy reinstatement, but Morgan Stanley cut its target to $33 just last week on margin concerns from AI infra costs — the Street itself is split on whether the growth story outruns the cost story, which is reason enough to wait rather than average in.`

- Position sizing: flag concentration risk if one holding dominates total portfolio value — that's a portfolio-construction call, independent of whether the stock itself looks good, and independent of both inputs above
- For positions deep in the red, don't let "already down a lot" alone drive a Trim call — that's sunk-cost reasoning. Base the call on forward-looking signals from either input, and if the honest case is "hold and wait for X catalyst," say exactly that rather than manufacturing a stronger opinion

**If neither search turns up a real signal** for a given name (routine price movement, no analyst activity, no real news that session), say so plainly — `**TICK** — 🟡 Hold — no strong signal either way today, news or analyst side` — rather than inventing a rationale to fill the line. Skip the opinion line entirely for watchlist names with zero notable news.

### Analyst Research Search

Run this as its own query, separate from the general news search, for every holding (always) and every watchlist name getting a My Take line:
```
"$TICKER" analyst rating OR price target OR upgrade OR downgrade 2026
```
Pull out: the consensus rating (e.g., "34 analysts rate it Buy"), the average 12-month price target and its % distance from the current price, and the single most recent *named-firm* action if one exists in the last 1–2 weeks. If search results only offer vague "analysts are optimistic" language with no firm name or number attached, treat that as no real signal for this input — don't launder vague sentiment into a specific-sounding claim.

**Format:** `**TICKER** — 🟢/🟡/🔴 [Add/Hold/Trim or Buy/Wait/Pass] — [1–2 sentence reasoning, citing the specific signal from this brief].`

**Required disclaimer**, shown once per brief directly above the "My Take" section, verbatim or close to it: *"Synthesized opinion from the news, analyst research, and price action above — not financial advice, and it doesn't know your full financial picture, risk tolerance, or tax situation. Weigh it as one input, not a directive."*

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

2. For each portfolio holding, get the current price using the two-source cross-check above, and search for any notable news from the past 24–48 hours (earnings, guidance changes, analyst moves, M&A, regulatory action). Compute value (`shares × price`), gain/loss vs. cost basis in dollars and percent, and today's change if available.

3. For each watchlist ticker, get the current price the same way (two-source cross-check — watchlist tickers get the same price rigor as holdings, since a wrong price is a wrong price either way), and search for the same categories of news — lighter treatment than holdings on the news side since there's no position at stake, but flag anything that would change whether it's worth buying (approaching target price, major catalyst).

4. Search for major index levels (S&P 500, Nasdaq, Dow) and any macro catalysts from the past 24 hours (Fed statements, CPI/jobs data, major geopolitical events affecting markets).

5. Only give a full story write-up (deep-dive treatment) to holdings or watchlist tickers with genuinely notable news — a stock with no news beyond routine price movement gets a single summary line, not a forced story section. Never pad with weak stories to fill space. For every ticker that does get a full write-up, use WebFetch on the actual article URL before composing its deep-dive paragraphs — this is mandatory, not optional, the same as the price cross-check. A WebSearch summary alone is fine for the initial news scan and for one-line mentions, but never as the sole basis for a full story section: search-tool summaries can flatten or misstate a detail (e.g. calling a completed deal "pending") in a way the real article wouldn't. If the fetch fails, retry with at least one alternate outlet (Reuters, AP, Bloomberg, CNBC, the company's own investor-relations press release) before falling back to the search snippet, and treat that fallback as the rare exception, not routine.

6. Run the Analyst Research Search for every holding, and for any watchlist name that will get a My Take line, then form the 🎯 My Take opinion for each — see "Investment Opinion" above for methodology, the two-input requirement, and the required disclaimer.

7. Compose the brief using the template below and display it.

**Full Brief template:**

```markdown
# 📈 Market Brief — [Month Day, Year]

## 💼 Portfolio Snapshot
| Ticker | Shares | Cost Basis | Price | Value | Gain/Loss | Day Change |
|--------|--------|------------|-------|-------|-----------|------------|
| [TICK] | [N]    | $[X.XX]    | $[X.XX] | $[X,XXX] | 🟢/🔴 [+/-]$[X] ([+/-]X.X%) | 🟢/🔴/⚪ [+/-]X.X% |

**Total value:** $[X,XXX.XX] | **Total gain/loss:** 🟢/🔴 [+/-]$[X,XXX] ([+/-]X.X%)

⚠️ Prices as of [H:MM AM/PM TZ], cross-checked across two sources — not a live brokerage feed. Confirm before trading.

---

[2–3 sentence narrative frame tying together what moved your portfolio, your watchlist, and the broader market today.]

---

## [Story headline for a holding or watchlist ticker with notable news]

**[1–2 sentence core fact]**

[Deep-dive paragraph 1: facts, numbers, quotes]

[Deep-dive paragraph 2: context, background, competitive/macro framing]

**Why it matters (for you):** [How this affects your position or watch decision specifically — not generic market commentary.]

🔗 [Source Name](url)

---

## 👀 Watchlist Highlights
- **[TICKER]** — [one-line note with a colored move indicator, e.g. "🔴 down 4% this week, now 6% above your $[target] target"]

---

## 🎯 My Take
_Synthesized opinion from the news, analyst research, and price action above — not financial advice, and it doesn't know your full financial picture, risk tolerance, or tax situation. Weigh it as one input, not a directive._

**Holdings:**
- **[TICK]** — 🟢/🟡/🔴 [Add/Hold/Trim] — [1–2 sentence reasoning tied to a specific signal above]

**Watchlist:** _(only names with a real signal — skip quiet ones)_
- **[TICK]** — 🟢/🟡/🔴 [Buy/Wait/Pass] — [1–2 sentence reasoning]

---

### 🔍 The Big Picture
[2 short paragraphs: the thread connecting today's moves, and what to watch next — earnings dates, Fed meetings, catalysts relevant to your specific holdings.]
```

---

## Mode 2: Portfolio Only (`portfolio`)

1. Read `finance/portfolio.md`. If it doesn't exist, say so and offer `add` or `import` to create the first holding.
2. Get current prices for each holding only, using the two-source cross-check above — skip watchlist and macro searches entirely.
3. Display just the Portfolio Snapshot table and total gain/loss from the template above (with the 🟢/🔴/⚪ indicators and the retrieval-time disclosure), plus one line per holding with any same-day news headline if one exists (no full deep-dive sections).
4. Run the Analyst Research Search for each holding, then add a 🎯 My Take section (Holdings only — no watchlist to opine on in this mode) with the required disclaimer, per "Investment Opinion" above.

---

## Mode 3: Watchlist Only (`watchlist`)

1. Read `finance/watchlist.md`. If it doesn't exist or is empty, say so and offer `watch <TICKER>` to add the first entry.
2. Get current price for each watchlist ticker using the two-source cross-check above, and search for recent news.
3. Display the 👀 Watchlist Highlights section only (with 🟢/🔴/⚪ indicators), noting current price vs. target price where a target is set.
4. Run the Analyst Research Search for each watchlist name, then add a 🎯 My Take section (Watchlist only) with the required disclaimer — only for names with a real signal from either step 2 or step 4, per "Investment Opinion" above.

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
5. If it's a held or watched ticker, run the Analyst Research Search and close with a single 🎯 My Take line (with the required disclaimer) — skip this entirely if it's neither, since there's no position or watch decision to opine on.

---

## Mode 6: Add Holding (`add <TICKER> <SHARES> @<PRICE>`)

1. Parse the ticker (uppercase), share count, and price (after `@`). If any piece is missing or unparseable, ask the user to clarify rather than guessing.
2. If `finance/` doesn't exist, create it. If `finance/portfolio.md` doesn't exist, create it from the template.
3. If the ticker already exists in the portfolio, average the cost basis: `new_cost = (old_shares × old_cost + new_shares × new_price) / (old_shares + new_shares)`, and sum the share counts. Otherwise append a new row.
4. Save with an updated `Last updated` timestamp and confirm: "Added [N] shares of [TICKER] @ $[price]. New position: [total shares] shares @ $[avg cost] avg."

---

## Mode 7: Remove Holding (`remove <TICKER>`)

1. Read `finance/portfolio.md`. If the ticker isn't found, say so.
2. Remove the row entirely (don't just zero it out — unlike routine-goals' tasks, there's no history value in keeping a closed position marked in this file).
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
- Prices come from direct WebFetch on two independent quote pages, never from WebSearch's aggregated snippets — see "Price Verification" above. Always include the "not a live feed" + retrieval-time disclosure in any output that shows a computed price or gain/loss.
- If a source's own page reports itself as delayed/stuck (an outage banner, not just a normal quote-delay disclaimer), treat its price as unusable for that fetch and fall back to a third source — don't silently average a stuck number in with a live one.
- Never pad a brief with a full story section for a holding that has no notable news — a summary line is enough.
- If a ticker is ambiguous (e.g., a common word), disambiguate with the exchange or full company name in the search query.
- Import (Mode 11) never writes without explicit user confirmation of the parsed preview — a misread digit in a screenshot could otherwise silently corrupt cost basis records.
- 🎯 My Take opinions must trace to a specific fact already in the brief (a cited move, catalyst, analyst call, or explicit valuation comment) — never fabricate a rationale to fill the line, and never omit the disclaimer. If a holding has no real signal that session, say so and default to Hold rather than inventing one.
- The Analyst Research Search is mandatory for every holding, not optional or "only if it comes up" — run it even on quiet days, since "no analyst has moved on this in weeks" is itself informative context for the My Take call.
