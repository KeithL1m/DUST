---
name: morning-coffee
description: Use when someone asks for world news, a global briefing, what's happening in politics or the global economy, or updates on a specific country or region.
argument-hint: [country or topic — optional] [follow <country/topic> | unfollow <country/topic>]
allowed-tools: Read, Write, WebSearch, WebFetch
disable-model-invocation: true
---

For the complete reference on coverage tiers, search strategy, story selection, output format details, and customization, see [reference.md](reference.md).

## What This Skill Does

An early-morning briefing on significant world news — politics, economies, and geopolitics. Deliberately excludes tech/AI industry news (see `/tech-update`) and individual stock moves (see `/market-brief`); this skill owns the political/policy story, not the market reaction to it.

Coverage is bucket-based, not a fixed country checklist — a set of always-covered major powers, a notable-only second tier, resource powers, and dynamic buckets (conflicts, central banks, international bodies) refreshed every run. A personal `follow` list adds guaranteed coverage for anything else you care about. The followed list is saved to `world/follow.md` so it persists across sessions.

## Modes

The first token of `$ARGUMENTS` selects a mode. If it doesn't match `follow` or `unfollow`, the entire argument is treated as a focus topic/country (or the whole thing is empty for a general roundup).

| Argument | Mode |
|----------|------|
| _(empty)_ | General world roundup across all coverage tiers |
| `<country/topic>` | Focused briefing on that country or topic |
| `follow <country/topic>` | Add to your followed list — guaranteed coverage going forward |
| `unfollow <country/topic>` | Remove from your followed list |

---

## Coverage Tiers

**Major powers — always covered, every run:**
US, China, Russia, UK, EU (as a bloc)

**Second tier — covered only when something genuinely notable happens:**
- Canada
- Vietnam
- Japan — plus a standing extra lens for economy, visa, and travel-policy news specifically, even below the usual "notable" bar for this tier

**Resource powers — covered only when something genuinely notable happens:**
Saudi Arabia, UAE

**Dynamic buckets — not fixed countries, re-evaluated every run:**
- Active conflict/diplomacy zones (whatever is currently happening — e.g., Middle East, Ukraine)
- Central banks & economic data (Fed, ECB, Bank of Canada, BoJ, PBOC; inflation/jobs/GDP releases)
- International bodies & summits (UN, G7/G20, WTO, IMF, major elections wherever active)

**Followed list (`world/follow.md`):** anything added via `follow` gets guaranteed coverage regardless of tier, the same way a major power does.

See [reference.md](reference.md) for the full search-query breakdown per tier and the "notable enough to include" bar for second-tier/resource-power stories.

---

## Mode 1: General Roundup _(default, no argument)_

1. Read `world/follow.md` if it exists, to fold followed countries/topics into this run's coverage.

2. Search recent stories (past 24–48 hours preferred; past week if thin) across every coverage bucket above — major powers unconditionally, second tier/resource powers/followed items only if something clears the notability bar (see reference.md), and the dynamic buckets fresh each time.

3. Select 5–8 stories that are genuinely significant — prioritize elections/political transitions, major legislation or policy shifts, central bank decisions, diplomatic developments, conflict developments, and major economic data releases. Skip opinion pieces, routine political commentary, and minor local news without national/international significance.

4. For each selected story, use WebFetch on the article URL for a richer description, relevant data points or quotes, and the article's main image URL (`og:image` meta tag or first prominent `<img>` tag). Political/mainstream news sites block WebFetch (403s, paywalls) more often than tech sites — if the primary source fails, retry with at least one alternate outlet (wire services like Reuters/AP/AFP, or BBC, Al Jazeera, NPR, the relevant national broadcaster, or an official government/institutional source tend to fetch reliably) before falling back to the WebSearch summary alone. A story built entirely from search snippets with no successful WebFetch should be the rare exception, not the default — it's the main reason stories come out thin.

5. Format the output using the template below.

## Output Format

```
---

# ☕ Morning Coffee — [Month Day, Year]

[2–3 sentence narrative framing the day's world-news landscape — the theme or tension running through the stories. Synthesize, don't list.]

---

## [Story Headline — present tense, active voice]

**[1–2 sentence summary: the core fact of what happened, for readers who skim.]**

[First deep-dive paragraph: 3–4 sentences covering what happened, who the key players are, and the specific details — numbers, dates, names, quotes.]

[Second deep-dive paragraph: 3–4 sentences covering context and background — why this is happening now, what led up to it, and how it fits the broader political/economic landscape.]

**Why it matters:** [3–4 sentences. Forward-looking analysis: what this changes, who's affected, what it signals, and what to watch next.]

🔗 [Source Name](article-url)

---

## [Next Story Headline]

...repeat for each story...

---

### 🔍 The Big Picture

[First paragraph: 3 sentences identifying the thread connecting all the stories.]

[Second paragraph: 3 sentences on what the pattern signals and what to watch closely in the coming days.]

---
```

**Summary line rules:**
- 1–2 sentences max. The core fact only — who did what.
- No context, no interpretation. That's what the deep-dive paragraphs are for.

**Deep-dive paragraph rules:**
- Split into two separate paragraphs with a blank line between them — never one dense block.
- First paragraph: 2–3 sentences of facts, details, data points, and direct quotes from the article — named officials/institutions, specific numbers, dates, vote counts. Not a vague summary.
- Second paragraph: 2–3 sentences of context and background — why this is happening now, what led up to it, how it fits the broader political/economic landscape.
- Every story needs at least 2 concrete data points across the two paragraphs (a figure, a date, a named quote, a vote count, etc.) — a paragraph with no specifics is too thin no matter how many sentences it has. Prioritize hitting this density bar over hitting the top of the sentence range — a tight 2-sentence paragraph packed with specifics beats a padded 3-sentence one.
- Write clearly enough that someone outside politics/economics can follow along.
- No editorializing — save interpretation for "Why it matters."

**Why it matters rules:**
- 3–4 sentences. Focus on implication and forward-looking significance.
- Answer: What does this change? Who's affected? What should someone watch next?

**Sourcing rules:**
- Always cite the source(s) actually used to build the story — same as tech-update, this is how the reader checks reliability.
- If more than one outlet was fetched to build a story (common here, since the neutrality rules below often require a second source for the other side's framing), list all of them: `🔗 [Source A](url-a), [Source B](url-b)`.
- Prefer named, identifiable outlets — wire services, established mainstream press, official/government sources — over aggregators, per the source-priority tiers in reference.md.

---

## Political Neutrality (required, every story)

This skill covers contested political topics by design — that makes neutrality a hard requirement, not a nice-to-have:

- Report what happened and who said what, sourced to named outlets — never assert a contested claim as settled fact.
- When a topic is genuinely disputed (elections, policy fights, conflicts), reflect that more than one side exists rather than presenting only one framing.
- The narrative frame, deep-dive paragraphs, and Big Picture must stay descriptive and analytical, never ideological — no partisan framing, no loaded language, no picking a "right side" of a contested issue.
- "Why it matters" may be forward-looking and analytical (who's affected, what changes, what to watch), but every claim in it must trace to a fact already established in the story — never a judgment about whether an outcome is good or bad.
- If a story is emotionally or politically charged, that's a reason for extra care in sourcing and phrasing, not a reason to soften or exaggerate the facts.

---

## Mode 2: Focused Topic (`<country/topic>`)

Triggered when the first argument token doesn't match `follow` or `unfollow`.

1. Treat the full argument string as the focus (country, region, or topic).
2. Search for news on that topic from the past 24–48 hours, falling back to the past week if thin.
3. Produce 2–4 story sections using the same format as the General Roundup, plus the same neutrality rules.
4. If there's genuinely no notable news on the topic, say so explicitly and offer to broaden the date range or the topic — never pad with weak stories to fill space.

---

## Mode 3: Follow (`follow <country/topic>`)

1. Parse everything after `follow` as the country/topic name.
2. If `world/` doesn't exist, create it. If `world/follow.md` doesn't exist, create it from the template below.
3. If the item is already followed, say so. Otherwise append a new row with today's date.
4. Save and confirm: "Now following [item] — it'll get guaranteed coverage in future roundups."

---

## Mode 4: Unfollow (`unfollow <country/topic>`)

1. Read `world/follow.md`. If the item isn't found, say so.
2. Remove the row.
3. Save and confirm: "Stopped following [item]."

---

## File Template

**`world/follow.md`:**

```markdown
# 🌍 Followed Countries & Topics

| Topic/Country | Added |
|----------------|-------|
| [Name] | [YYYY-MM-DD] |

_Updated: [YYYY-MM-DD]_
```

## Notes

- Always read `world/follow.md` before a general roundup — never guess what's on someone's followed list.
- If `world/` doesn't exist, create it the first time `follow` runs.
- Major powers (US, China, Russia, UK, EU) get coverage every single run regardless of how quiet the news is that day — if genuinely nothing happened, a single summary line is fine, but don't skip the tier entirely.
- Second tier, resource powers, and followed items only get a story if something clears the notability bar (see reference.md) — don't force weak stories in just because a country is on the list.
- Japan's economy/visa/travel-policy lens is a standing exception to the notability bar for that specific topic area — a moderately significant visa-policy change is worth including even if it wouldn't otherwise clear the bar.
- Never fabricate a story or a data point. If a WebFetch fails, use only what the search result itself provided.
- Avoid duplicating the same event across multiple sources — pick the most authoritative source and move on.
- Keep headlines in present tense, active voice: "Bank of Canada Holds Rates Steady," not "Rates Were Held Steady by the Bank of Canada."
