# Morning Coffee Reference

Complete reference for the morning-coffee skill. Covers argument usage, coverage tiers, search strategy, story selection, output format, the neutrality guardrail, customization, and troubleshooting.

---

## Argument Reference

`$ARGUMENTS` is optional. The first token decides the mode.

| Input | Behavior |
|-------|----------|
| _(empty)_ | General roundup across all coverage tiers |
| `follow <country/topic>` | Add to the followed list (`world/follow.md`) |
| `unfollow <country/topic>` | Remove from the followed list |
| Anything else | Treated as a focused country/topic briefing |

**Examples:**
```
/morning-coffee                    → General world roundup
/morning-coffee Japan              → Focused briefing on Japan
/morning-coffee inflation          → Focused briefing on inflation news globally
/morning-coffee follow Brazil      → Add Brazil to the followed list
/morning-coffee unfollow Brazil    → Remove Brazil from the followed list
```

**Argument behavior notes:**
- Arguments are space-delimited. `follow South Korea` parses `follow` as the command and `South Korea` as the item.
- A focused topic can be a country, a region ("Middle East"), or a subject ("central bank policy") — treated the same way.
- If a focused topic has no recent news, say so explicitly and offer to broaden to the past week.

---

## Coverage Tiers in Detail

### Major powers (always covered)

US, China, Russia, UK, EU. These get a check every single run, no exceptions. If genuinely nothing notable happened for one of them that day, a single summary line ("Quiet day for Russia — no major developments") is acceptable; skipping the tier's check entirely is not.

**Why these five:** roughly UN Security Council permanent membership (US, China, Russia, UK, France) with France folded into the EU bloc rather than tracked separately, since EU-level policy/trade/foreign-affairs moves are usually the more relevant unit of coverage than any single member state.

### Second tier (notable-only)

Canada, Vietnam, Japan. Only included if a story clears the notability bar below — don't force a story in just because the country is on the list.

**Japan's standing exception:** independent of the general notability bar, Japan gets a permanent extra lens for economy, visa, and travel-policy news specifically. A moderately significant change here (e.g., a new visa category, a shift in tourist entry rules, a Bank of Japan rate move) is worth including even if it wouldn't otherwise be "notable" enough for a second-tier country. This lens does not extend to Canada or Vietnam.

### Resource powers (notable-only)

Saudi Arabia, UAE. Same notability bar as the second tier. These are tracked separately from "major powers" because their global significance is concentrated in energy/capital markets and increasingly sovereign AI/tech investment, rather than broad geopolitical weight — a story here should usually tie back to oil/energy policy, sovereign wealth activity, or a major foreign investment, not routine domestic news.

### Dynamic buckets (not fixed countries)

Re-evaluated fresh every run rather than tracked as a standing list:
- **Active conflict/diplomacy zones** — wherever the news actually is (Middle East, Ukraine, or wherever else is active at the time). Don't hardcode a region here; let the search surface what's currently live.
- **Central banks & economic data** — Fed, ECB, Bank of Canada, Bank of Japan, PBOC; major inflation/jobs/GDP releases from any major power.
- **International bodies & summits** — UN, G7/G20, WTO, IMF, and major elections wherever they're happening (not limited to the tracked countries — a major election anywhere in the world is fair game).

### Followed list (`world/follow.md`)

Anything a user adds via `follow` gets the same guaranteed-coverage treatment as a major power, not the notable-only treatment of the second tier. The assumption is: if someone bothered to follow it, they want to hear about it even on a quiet day.

---

## Search Strategy

### Date Range Logic

1. **Past 24 hours** — Try first.
2. **Past 48 hours** — Expand if 24-hour results are thin.
3. **Past week** — Fall back only for a tier/topic with genuinely nothing recent.
4. **Always disclose** the date range used when it's wider than 48 hours.

### Constructing Search Queries

Run separate searches per bucket rather than one broad query — this avoids one loud story (e.g., a US story) crowding out coverage of a quieter tier.

| Bucket | Example query |
|--------|--------------|
| Major powers | `"United States" OR "China" OR "Russia" OR "United Kingdom" OR "European Union" politics OR policy 2026` |
| Second tier | `"Canada" OR "Vietnam" OR "Japan" politics OR policy OR election 2026` |
| Japan's standing lens | `Japan visa OR "travel policy" OR economy 2026` |
| Resource powers | `"Saudi Arabia" OR "UAE" oil OR energy OR "sovereign wealth" OR investment 2026` |
| Conflict/diplomacy | `war OR conflict OR ceasefire OR sanctions 2026` (let results reveal the active region) |
| Central banks/economic data | `Federal Reserve OR ECB OR "Bank of Canada" OR "Bank of Japan" inflation OR rates OR jobs 2026` |
| International bodies | `UN OR G7 OR G20 OR WTO OR IMF summit OR ruling 2026` |
| Followed items | `"$FOLLOWED_ITEM" news 2026` — one query per item on the list |

### Source Priority

**Tier 1 — Primary sources:**
- Government/official statements, central bank releases, court/tribunal rulings
- Official statistics agencies for economic data

**Tier 2 — Established journalism:**
- Reuters, AP, BBC, Financial Times, The Economist
- Bloomberg (economics/markets angle)

**Tier 3 — Secondary/aggregator (context only):**
- Regional outlets not independently verified
- Analysis/explainer pieces without a fresh news hook

**Avoid:**
- Opinion columns, op-eds, punditry
- Sources with an evident partisan slant presented without acknowledging it
- Stories older than a week unless they resurfaced with new context

---

## Story Selection Criteria

### What to Include

A story is worth including if it meets at least one of these:

- **Election or political transition** — a result, a major shift in a race, a leadership change
- **Major legislation or policy shift** — a bill passed/blocked, a major regulatory change
- **Central bank decision** — a rate move, a major policy statement
- **Diplomatic development** — a treaty, a summit outcome, sanctions imposed or lifted
- **Conflict development** — escalation, ceasefire, a major military or humanitarian development
- **Major economic data** — inflation, jobs, GDP releases from a major power, especially surprises vs. forecast
- **Major court/tribunal ruling** — with national or international significance

### What to Skip

- Routine political commentary or punditry
- Opinion columns, op-eds
- Minor local political news without national/international significance
- Stories older than a week unless they resurfaced with new context
- Duplicate coverage of the same event across multiple sources — pick the most authoritative one

### Story Count

- **General roundup:** 5–8 stories. Fewer is fine if quality would otherwise drop.
- **Focused briefing:** 2–4 stories. If fewer than 2 quality stories exist, say so rather than padding.
- Never force a story in just to represent every tier — a quiet day for a given bucket gets a one-line note, not a manufactured story.

---

## Output Format Reference

Same three-layer structure as `tech-update`: a dated narrative frame, per-story deep-dive sections, and a closing Big Picture. See SKILL.md for the full template.

**Headline rules:** present tense, active voice, under 10 words, no clickbait. "Bank of Canada Holds Rates Steady," not "Rates Were Held Steady by the Bank of Canada."

**Narrative frame (2–3 sentences):** the theme or tension connecting the day's stories — synthesize, don't list. A frame that could apply to any day's news is too vague; make it specific to today's actual stories.

**Big Picture (two paragraphs):** first paragraph identifies the connecting thread; second covers what the pattern signals and what to watch in the coming days. This is synthesis, not a recap of the stories already told.

---

## Political Neutrality — Worked Examples

This is the one hard requirement that doesn't have an equivalent in `tech-update` or `market-brief`, since neither covers genuinely contested political topics the way this skill does.

**Good — states what happened and who said what, without asserting a contested claim as fact:**
> The bill passed the lower house 218–210 along largely party lines; proponents argue it will reduce the deficit by $200B over a decade, while critics, including the Congressional Budget Office's independent estimate, project a $340B increase — a discrepancy stemming from different assumptions about growth effects.

**Bad — asserts a contested economic/political claim as settled:**
> The bill will responsibly cut the deficit while critics needlessly fearmonger about costs.

**Good — reflects that a conflict has more than one narrative:**
> Each side blamed the other for the ceasefire's collapse; independent verification of the triggering incident was not available at time of writing.

**Bad — picks a side in an active, disputed conflict:**
> The ceasefire collapsed because [Party A] violated the terms first.

**Good — forward-looking "Why it matters" grounded in stated facts:**
> This is the first rate cut in 14 months, which eases borrowing costs for the roughly 40% of households with variable-rate mortgages — but the central bank's own statement flagged persistent inflation risk, meaning further cuts aren't guaranteed. Watch the next inflation print for whether this was a one-off or the start of a cutting cycle.

**Bad — injects a value judgment not grounded in a stated fact:**
> This rate cut proves the central bank finally got its priorities right after months of unnecessary tightening.

---

## Customization

### Adjusting Coverage Tiers

Edit the "Coverage Tiers" section in SKILL.md directly — add/remove/re-tier a country any time. This is plain text; no rebuild needed. A country can move tiers (e.g., promote Canada to "always covered") by moving its name between the tier lists.

### Adjusting the Notability Bar

The "What to Include" criteria in this file double as the notability bar for second-tier/resource-power/followed items. Loosen or tighten these if the roundup feels too thin or too padded.

### Adjusting Story Count

Change "5–8 stories" in SKILL.md Mode 1 Step 3 to whatever fits — a quick morning digest might work better at 3–4.

### Scheduling as a Daily Briefing

Like `tech-update`, `/schedule` can run `/morning-coffee` on a recurring basis — e.g., every morning before your day starts. Note that `follow`/`unfollow` are interactive management commands and aren't good candidates for unattended scheduling — only schedule the general roundup or a fixed focused topic.

### Running as a Subagent

To keep the web search out of your main conversation context, add `context: fork` and `agent: general-purpose` to the frontmatter — the skill runs in isolation and returns only the finished briefing. Trade-off: you lose the ability to ask follow-up questions about the briefing in the same thread.

---

## Troubleshooting

### A major power has almost nothing to report

This is expected occasionally — not every day produces major news from every one of the five major powers. Use a single summary line rather than skipping the tier or forcing a weak story in.

### A followed item never shows up

Check `world/follow.md` exists and the item's name matches what you search for — a typo or overly specific name (e.g., "Ho Chi Minh City" instead of "Vietnam") can cause weak search results. Try broadening the followed term.

### The briefing feels one-sided on a political story

Re-check the neutrality section above — specifically whether the story sourced only one outlet or one side's framing. Re-run the search including outlets more likely to carry the other side's statements, and revise to reflect that a dispute exists rather than resolving it.

### Japan's visa/economy stories feel too minor compared to other second-tier coverage

That's intentional — Japan's standing lens has a lower bar than the rest of the second tier by design. If it's producing too much low-value content, tighten the lens's own bar in SKILL.md Mode 1 Step 3 rather than removing it entirely.

### A story feels thin or reads like a summary instead of a briefing

This almost always means the primary WebFetch failed and the story got built from the WebSearch tool's auto-generated snippet instead of the actual article. Mainstream/political outlets (The Hill, Bloomberg, AOL, most wire-service aggregators) 403 non-browser fetches far more often than tech sites do, so this happens more here than in `tech-update`.

Fix: when the primary source fails, don't accept the search-snippet summary as final — retry WebFetch against an alternate outlet covering the same story. Outlets that tend to fetch reliably: Reuters, AP, AFP, BBC, Al Jazeera, NPR, Kyiv Independent, SCMP, and official government/institutional pages (`.gov`, central bank sites, parliament sites). A story is allowed to end up snippet-only occasionally, but if it's happening on most stories in a roundup, that's the signal to keep retrying rather than move on.

### Skill triggers when it shouldn't

`disable-model-invocation: true` is already set, since `follow`/`unfollow` write files — it won't auto-trigger from casual conversation, only from `/morning-coffee` directly. If you want natural-language triggering instead, remove that field (this also re-enables auto-triggering for the read-only roundup/focused modes).

### Skill doesn't trigger when it should

Invoke directly with `/morning-coffee` to confirm the skill loads, and check `/context` to confirm the skill's description is within the character budget alongside your other skills.
