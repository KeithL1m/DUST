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

### Constructing Search Queries

For a specific ticker or holding:
```
"$TICKER" stock price today
"$TICKER" OR "$COMPANY_NAME" earnings OR guidance OR analyst 2026
```

For the macro roundup:
```
"S&P 500" OR "Nasdaq" OR "Dow" today
Federal Reserve OR "interest rates" OR CPI OR jobs report 2026
```

### Source Priority

**Tier 1 — Primary sources:**
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

Web search results can lag real-time quotes, especially intraday. Always keep the "not a live feed" disclosure visible, and if a price looks clearly wrong (e.g., doesn't match a recent split), say so rather than silently using it.

### Import misclassifies portfolio vs. watchlist

Ask the user directly rather than re-guessing from the same image — a second automated guess has the same failure mode as the first.

### Cost basis looks wrong after `add`

Check whether the average-cost formula in Mode 6 was applied correctly: `(old_shares × old_cost + new_shares × new_price) / (old_shares + new_shares)`. If the user meant to correct a mistaken entry rather than record a new purchase, use `update` instead, which replaces rather than averages.

### Skill only runs on explicit invocation

`disable-model-invocation: true` is set in the frontmatter, since this skill touches personal financial data — it will never auto-trigger from casual conversation ("how's my portfolio doing"), only from typing `/market-brief` directly. To allow natural-language triggering instead, remove that field.
