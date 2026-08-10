# Market Brief Reference

Complete reference for the market-brief skill. Covers argument usage, search strategy, story selection, output format, import behavior, customization, and troubleshooting.

---

## Argument Reference

`$ARGUMENTS` is optional. How it's used depends on the first token.

| Input | Behavior |
|-------|----------|
| _(empty)_ | Full brief — portfolio + watchlist + macro roundup |
| `portfolio` | Portfolio-only snapshot |
| `watchlist` | Watchlist-only news |
| `macro` | Indices/Fed/rates only |
| Anything else | Treated as a focused ticker/company/sector topic |
| `add` / `remove` / `update` / `watch` / `unwatch` / `import` | Management commands (see SKILL.md Modes 6–11) |

**Argument behavior notes:**
- Arguments are space-delimited. Command keywords are matched against the first token only — `add AAPL 10 @150` parses `add` as the command and the rest as its parameters.
- Ticker symbols are case-insensitive for search purposes but should be normalized to uppercase when stored.
- If a focused ticker has no recent news, say so explicitly and offer to broaden to the past week or to the sector.

---

## Search Strategy

### Date Range Logic

1. **Past 24 hours** — Try first. Markets move faster than general tech news, so recency matters more here.
2. **Past 48 hours** — Expand if 24-hour results are thin.
3. **Past week** — Fall back only for a ticker with genuinely no recent activity.
4. **Always disclose** the date range used when it's wider than 48 hours.

### Getting Prices vs. Getting News

These use different tools and different sourcing rules — don't conflate them:

- **Prices** never come from WebSearch. Use direct WebFetch on two independent quote pages per the "Price Verification" section in SKILL.md (Google Finance + stockanalysis.com, with Yahoo Finance/CNBC as a tiebreaker). WebSearch's AI-summarized snippets pull from multiple cached/stale pages at once and are the least trustworthy source for a number that needs to be exact.
- **News** (earnings, guidance, M&A, analyst moves, macro) is fine via WebSearch — a summarized snippet of "what happened" degrades much more gracefully than a summarized snippet of "what number is it right now."

### Constructing Search Queries

For a specific ticker or holding's news:
```
"$TICKER" OR "$COMPANY_NAME" earnings OR guidance OR analyst 2026
```

For the macro roundup:
```
"S&P 500" OR "Nasdaq" OR "Dow" today
Federal Reserve OR "interest rates" OR CPI OR jobs report 2026
```

For prices, skip search entirely — go straight to:
```
https://www.google.com/finance/quote/<TICKER>:<EXCHANGE>
https://stockanalysis.com/stocks/<ticker>/
```

### Source Priority

**For prices (WebFetch only, see above):**
1. Google Finance and stockanalysis.com — the two-source default
2. Yahoo Finance or CNBC — tiebreaker if the two above disagree or either is flagged as delayed/stuck

**For news (Tier 1 — primary sources):**
- SEC filings (8-K, 10-Q, earnings releases)
- Company investor relations press releases
- Federal Reserve / BLS releases for macro data

**Tier 2 — Established financial journalism:**
- Reuters, Bloomberg, WSJ, Financial Times
- CNBC, Yahoo Finance for earnings/price coverage

**Tier 3 — Secondary / aggregator (context only):**
- Seeking Alpha, Motley Fool style analysis pieces
- Aggregator sites that don't cite a primary source

**Avoid:**
- Using WebSearch (rather than direct WebFetch) for any number that ends up in the Portfolio Snapshot table or a gain/loss figure
- Pure technical-analysis or price-prediction pieces without a stated catalyst
- Sources with paywalls you can't fetch — note the gap rather than guessing at content
- Any single-day move under ~2% with no identifiable news hook

---

## Story Selection Criteria

### What to Include

- **Earnings or guidance change** — actual or revised figures
- **Fed/macro data release** — rate decisions, CPI, jobs reports
- **M&A or major partnership** involving a held/watched ticker or its sector
- **Regulatory action** — investigation, ruling, fine
- **Analyst upgrade/downgrade** with a stated rationale, not just a price target change
- **>5% single-day move** with an identifiable cause

### What to Skip

- Routine price movement with no news hook — goes in the snapshot table only, not a story section
- Speculative "could move the stock" pieces without a stated catalyst
- Pure technical-analysis commentary (chart patterns, support/resistance) with no fundamental news
- Stories older than a week unless they resurfaced with new context

### Story Count

- **Full brief:** Only write full story sections for holdings/watchlist tickers with genuinely notable news — this could be 0–5 depending on the day. A quiet day with just the snapshot table and a one-line watchlist note is a valid, complete brief.
- Never pad with a forced story section just to make the brief feel fuller.

---

## Output Format Reference

See SKILL.md for the full templates. Key rules:

**Headlines:** Present tense, active voice, under 10 words. "Apple Beats on Cloud Revenue," not "Cloud Revenue Beat Was Reported by Apple."

**Summary line:** 1–2 sentences, the core fact only.

**Deep-dive paragraphs:** Two paragraphs — first is facts/numbers/quotes, second is context/background. No editorializing in either; save interpretation for "Why it matters."

**"Why it matters (for you)":** This differs from tech-update's version — it must tie back to the user's actual position or watch decision, not generic market commentary. Example: "This narrows your unrealized gain from 18% to 11% — still comfortably above your cost basis, but worth watching if the guidance cut persists next quarter." Not: "This is bad news for tech stocks broadly."

**Portfolio Snapshot table:** Always shown first in the Full Brief and Portfolio modes, even if no story sections follow. Always includes the "not a live feed" disclosure directly beneath it.

**Rising/falling indicators:** Every gain/loss and day-change figure gets a colored-dot prefix since plain markdown can't render font color: 🟢 positive, 🔴 negative, ⚪ flat/unchanged (~±0.05% or explicitly reported unchanged). Applies to the Portfolio Snapshot table, the total gain/loss line, Watchlist Highlights bullets, and any price move cited inside a story section.

**🎯 My Take (worked examples):** See "Investment Opinion" in SKILL.md for the full methodology. Good vs. bad calls, illustrated:

- Good — traces to a fact already in the brief: `**FIG** — 🟡 Hold — Today's 8% jump on the BofA Buy reinstatement is a real signal, but it's one session after a long slide; the honest call is "let the bounce prove itself" rather than adding or trimming on one green day.`
- Good — names concentration risk independent of the stock's own merits: `**NVDA** — 🟡 Hold — Nothing here changes the thesis, but NVDA is already ~60% of your portfolio value; any new money probably belongs elsewhere before adding more here.`
- Good — admits there's no real signal instead of inventing one: `**AAPL** — 🟡 Hold — No strong signal either way today beyond the routine ex-dividend dip; nothing here to act on.`
- Bad — sunk-cost reasoning: `**FIG** — 🔴 Trim — it's already down 68%, cut the loss.` (The 68% figure alone isn't forward-looking information; it says nothing about what happens next. If the honest case IS defensive, ground it in something forward-looking instead — e.g. a specific guidance concern or valuation call from a cited source.)
- Bad — invented precision: `**NVDA** — 🟢 Add — technicals suggest a bounce to $230 is likely.` (No source in this brief said that; don't manufacture a price target or technical call that wasn't actually reported.)

---

## Import Behavior Detail

### Recognizing Screenshot Layouts

Brokerage apps vary widely (Robinhood, Fidelity, Schwab, E*TRADE, etc.). Common patterns:

- **Portfolio/positions view:** Usually shows ticker, shares owned, average cost, current price, and total gain/loss — this combination signals **portfolio**.
- **Watchlist view:** Usually shows ticker, current price, and day change only, with no share count — this combination signals **watchlist**.

When the layout is ambiguous (e.g., a screenshot cropped to only show ticker and price), state the ambiguity and ask the user directly rather than guessing.

### Confirmation Flow

1. Extract → 2. Classify (portfolio vs. watchlist) → 3. Show preview table → 4. User confirms or corrects → 5. Merge into the target file using existing add/watch logic → 6. Confirm what was saved.

Steps 3–4 are mandatory and cannot be skipped, even if the user has previously confirmed an import — each screenshot is a new opportunity for a misread digit.

### What Never Happens

- The screenshot file itself is never copied into `finance/` or referenced by path in the saved markdown.
- No cost basis or share count is ever inferred from partial data (e.g., "total value" without a share count doesn't imply a price).
- No holding is auto-removed based on absence from a screenshot — an import only adds/updates what's visible; use `remove` explicitly to close out a position.

---

## Customization

### Adjusting Story Thresholds

If the default "5% single-day move" or "24-48 hour" window feels too noisy or too quiet, adjust the thresholds in the Story Selection Criteria section above and in SKILL.md Mode 1, Step 5.

### Multi-Currency Support

The current design assumes USD. To support other currencies, add a `Currency` column to both file templates and thread it through the value/gain-loss calculations in Mode 1.

### Scheduling as a Daily Briefing

Like tech-update, `/schedule` can run `/market-brief` on a recurring basis (e.g., pre-market on weekdays). Note that `add`/`remove`/`update`/`watch`/`unwatch`/`import` are interactive by nature and aren't good candidates for unattended scheduling — only schedule the read-only modes (default, `portfolio`, `watchlist`, `macro`, or a focused ticker).

---

## Troubleshooting

### Prices seem stale or wrong

Live quote pages can lag real-time prices, especially intraday, and can also fail silently — a page can display a normal-looking timestamp while its own banner admits it's serving delayed data (this has happened with Yahoo Finance). This is exactly what the two-source cross-check in SKILL.md exists to catch: if one source disagrees with a second by more than ~0.5%, don't average them or guess which is right — check for a delayed/outage disclaimer on either page, drop the stuck one, and pull a third source as tiebreaker. Always keep the "not a live feed" + retrieval-time disclosure visible, and if a price still looks clearly wrong after cross-checking (e.g., doesn't match a recent split), say so rather than silently using it.

### Import misclassifies portfolio vs. watchlist

Ask the user directly rather than re-guessing from the same image — a second automated guess has the same failure mode as the first.

### Cost basis looks wrong after `add`

Check whether the average-cost formula in Mode 6 was applied correctly: `(old_shares × old_cost + new_shares × new_price) / (old_shares + new_shares)`. If the user meant to correct a mistaken entry rather than record a new purchase, use `update` instead, which replaces rather than averages.

### Skill only runs on explicit invocation

`disable-model-invocation: true` is set in the frontmatter, since this skill touches personal financial data — it will never auto-trigger from casual conversation ("how's my portfolio doing"), only from typing `/market-brief` directly. To allow natural-language triggering instead, remove that field.
