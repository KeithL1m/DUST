# Tech Update Reference

Complete reference for the tech-update skill. Covers argument usage, search strategy, story selection, output format, customization, and troubleshooting.

---

## Argument Reference

`$ARGUMENTS` is optional. How it's used depends on what the user passes.

| Input | Behavior |
|-------|----------|
| _(empty)_ | General roundup — AI, major companies, hardware, industry moves |
| Company name | Focused on that company (e.g., `OpenAI`, `Apple`, `NVIDIA`) |
| Topic | Focused on that subject area (e.g., `AI hardware`, `open source models`, `regulation`) |
| Multi-word phrase | Treated as a single topic — no positional splitting |

**Examples:**

```
/tech-update                    → General roundup
/tech-update OpenAI             → OpenAI news only
/tech-update AI regulation      → Regulatory/policy angle
/tech-update semiconductor      → Chip industry focus
/tech-update Anthropic Claude   → Specific company + product
```

**Argument behavior notes:**
- Arguments are space-delimited. `AI regulation` is passed as two tokens but treated as one topic in the skill.
- If a company has no news in the past 48 hours, say so explicitly and offer to broaden to the past week or to a related topic.
- Arguments are case-insensitive for search purposes — `openai` and `OpenAI` produce the same results.

---

## Search Strategy

### Date Range Logic

Always prefer recency. Apply this cascade:

1. **Past 24 hours** — Try this first. If 3+ quality stories exist, stop here.
2. **Past 48 hours** — Expand if 24-hour results are thin.
3. **Past week** — Fall back only if the topic is niche or the company had no recent activity.
4. **Always disclose** the date range used when it's wider than 48 hours.

### Constructing Search Queries

For focused searches, combine the argument with recency signals:

```
"$ARGUMENTS" + "news" + current month/year
"$ARGUMENTS" + "announced" OR "released" OR "launched"
"$ARGUMENTS" + "2025"
```

For general roundups, run separate searches per category and merge:

| Category | Example query |
|----------|--------------|
| AI / LLMs | `"AI" "model" "released" OR "announced" 2025` |
| Major tech companies | `"Google" OR "Microsoft" OR "Meta" "announced" 2025` |
| Chinese tech | `"Baidu" OR "Alibaba" OR "Tencent" OR "Huawei" OR "ByteDance" OR "DeepSeek" 2025` |
| Hardware / chips | `"NVIDIA" OR "AMD" OR "semiconductor" "2025"` |
| Industry / business | `"acquisition" OR "funding" OR "layoffs" "tech" 2025` |
| Regulation / policy | `"AI regulation" OR "antitrust" "tech" 2025` |

### Source Priority

Prefer primary or high-signal sources. Ranked by reliability:

**Tier 1 — Primary sources (most credible):**
- Official company blog posts and press releases
- SEC filings, earnings call transcripts
- Peer-reviewed research (arXiv, conference papers)

**Tier 2 — Established tech journalism:**
- The Verge, Wired, Ars Technica, MIT Technology Review
- Reuters, Bloomberg, Financial Times (tech coverage)
- TechCrunch (funding/startup news)

**Tier 3 — Secondary / aggregator (use for context, not as the primary citation):**
- Hacker News (signal for community interest)
- Twitter/X threads from known researchers
- Tech newsletters (The Information, Import AI)

**Chinese tech sources:**
- Official company blogs and Weibo/WeChat announcements (primary)
- South China Morning Post, Caixin, 36Kr (English editions where available)
- Reuters/Bloomberg China tech desk for international-facing coverage

**Avoid:**
- Opinion/editorial pieces without factual news hooks
- Sources with paywalls you can't fetch
- Aggregators that don't cite primary sources

---

## Story Selection Criteria

### What to Include

A story is worth including if it meets at least one of these:

- **Announcement / launch** — A new product, model, API, or service is released or publicly announced
- **Major funding or acquisition** — Rounds above $50M, or acquisitions that shift competitive dynamics
- **Major investment or partnership between major companies** - A collaboration or partnership between major companies
- **Research breakthrough** — A paper or finding that meaningfully changes what's possible
- **Regulatory or legal event** — A ruling, fine, investigation, or new law affecting tech
- **Significant leadership or org change** — C-suite moves, major layoffs, restructuring at well-known companies
- **Industry-shifting partnership** — A deal or integration between major players

### What to Skip

- Speculation, rumor, or "sources say" stories without substantiation
- Minor product updates (e.g., a version bump, a UI tweak)
- Opinion columns, op-eds, commentary pieces
- Stories that are more than a week old unless they resurfaced with new context
- Duplicate coverage of the same event — pick the best source only

### Story Count

- **General roundup:** 5–8 stories. Fewer is better if quality drops.
- **Focused search:** 3–5 stories. If fewer than 3 quality stories exist, say so.
- Never pad with weak stories to hit a count. A strong 4-story briefing beats a diluted 8-story one.

---

## Output Format Reference

The output has three structural layers: a dated narrative frame, per-story sections with deep dives, and a closing big-picture paragraph.

### Layer 1: Narrative Frame (2–3 sentences)

Opens the briefing under a dated `# 🗞 Tech Briefing` header. Sets the "headline mood" — the theme or tension running through the day's news. Synthesize, don't list.

**Good:**
> The week of May 20 is defined less by model launches and more by structural bets: who controls the hardware, who controls the platform, and who's trimming to survive the next leg of the race.

**Bad:**
> Here are today's tech news stories. There are 6 stories today.

### Layer 2: Story Sections

Each story gets its own `##` section separated by `---`, with a skimmable summary line, two deep-dive paragraphs, a "Why it matters" block, and a source link.

**Full template:**

```markdown
---

## [Story Headline — present tense, active voice]

**[1–2 sentence summary of the core fact — for readers who skim.]**

[First deep-dive paragraph: 3–4 sentences on the specific facts — what happened, who the key players are, numbers, dates, product names, and any notable quotes.]

[Second deep-dive paragraph: 3–4 sentences on context and background — why this is happening now, what led up to it, how it fits the competitive landscape.]

**Why it matters:** [3–4 sentences on forward-looking implication — what changes, who wins or loses, what to watch.]

🔗 [Source Name](article-url)
```

**Headline rules:**
- Present tense, active voice: "OpenAI Ships GPT-5" not "GPT-5 Was Released by OpenAI"
- No clickbait. State the fact.
- Under 10 words.

**Summary line rules:**
- 1–2 sentences. The core fact only — who did what.
- No context or interpretation. That belongs in the deep-dive paragraphs.

**Deep-dive paragraph rules:**
- Always two separate paragraphs with a blank line between — never one dense block.
- First paragraph: facts, data points, quotes. Second paragraph: context, background, competitive framing.
- Write clearly enough that someone outside tech can follow. No assumed knowledge.
- No editorializing — save interpretation for "Why it matters."

**Why it matters rules:**
- 3–4 sentences. Forward-looking, not a restatement of the description.
- Answer: What changes? Who wins or loses? What should someone watch next?

**Example story section:**

```markdown
---

## Anthropic Raises $2B Series E at $18B Valuation

**Anthropic closed a $2 billion funding round led by Google, valuing the company at $18 billion.**

Anthropic's Series E was announced May 15 and led by Google with participation from Spark Capital and existing investors, bringing total funding to $7.3 billion. The $18 billion post-money valuation is a significant jump from its 2024 raise, and CEO Dario Amodei stated the capital will go primarily toward compute infrastructure, safety research, and scaling Claude's enterprise deployments.

The round comes as Anthropic has been aggressively growing its customer base across financial services, legal, and healthcare — sectors where trust and explainability matter most. It also deepens the company's partnership with Google, which includes Anthropic running workloads on Google Cloud's TPU infrastructure, creating mutual dependency that goes beyond a simple investor relationship.

**Why it matters:** The raise cements Anthropic as the primary alternative to OpenAI for enterprise buyers who want a safety-focused vendor. The continued investor appetite — despite extraordinarily high compute costs — signals the market still believes frontier AI has durable commercial upside. For competitors, this means Anthropic has the runway to match OpenAI on capability for at least another 18–24 months. Watch whether the Claude 4 launch converts this capital into measurable market share.

🔗 [TechCrunch](https://techcrunch.com/example)
```

### Layer 3: Big Picture (two paragraphs)

Closes the briefing under a `### 🔍 The Big Picture` subheading. Split into two paragraphs: the first identifies the thread connecting all the stories; the second covers what the pattern signals about where the industry is heading and what to watch in the coming week. This is synthesis — not recap.

**Good:**
> The throughline this week is control — over chips, over platforms, over cost. Meta is betting on compute over headcount. Apple is betting on ecosystem over model ownership. China is betting on domestic silicon over NVIDIA dependency. The industry is no longer just racing to build the best model; it's racing to lock in the infrastructure layer that everything else runs on. Watch Google I/O's hardware announcements this week — if Google ships a new TPU generation alongside Gemini updates, it signals a vertical integration play that would directly challenge NVIDIA's dominance at the data center level.

**Bad:**
> Those were today's tech updates. Hope you found them useful!

---

## Customization

### Adding a Standing Focus

If you always want certain categories covered in the general roundup, edit SKILL.md to add them to Step 2. For example, to always include crypto/web3:

```markdown
2. Search for recent stories across:
   - AI and LLMs
   - Major tech companies (Apple, Google, Meta, Microsoft, Amazon)
   - Hardware and chips
   - Industry moves (funding, acquisitions, layoffs)
   - Crypto and web3
```

### Adjusting Story Count

Change "5–8 stories" in SKILL.md to whatever fits your preferences. A morning standup digest works well at 3–4; an end-of-week roundup works at 8–10.

### Scheduling as a Daily Briefing

The `/schedule` skill can run `/tech-update` on a recurring basis — e.g., every weekday morning. Once scheduled, it runs as a background agent and posts results without you having to invoke it manually.

### Combining with Other Skills

Tech-update can feed into other skills. Example: run `/tech-update AI regulation`, then pass the output to a summarizer or a note-taking skill that saves to a file. The output is plain text, so it composes cleanly.

---

## Troubleshooting

### Results are sparse or outdated

1. **Broaden the date range** — Search the past week instead of 48 hours.
2. **Check the topic specificity** — Very niche topics (e.g., a small startup) may have infrequent coverage. Offer to broaden to the parent company or sector.
3. **Try alternate search terms** — The company may be known by a different name in press coverage.
4. **Say so explicitly** — Never pad with old or weak stories. Tell the user: "Not much has happened with X in the past week — here's the last notable story from [date]."

### Results are off-topic

1. **The argument may be ambiguous** — "Apple" could mean the company or the fruit in some search contexts. Use more specific queries: `"Apple Inc" news 2025`.
2. **Too many search categories at once** — For general roundups, run separate searches per category rather than one broad query.

### Output feels repetitive across stories

- Multiple stories may be covering the same underlying event from different angles. Merge them into one bullet using the most authoritative source.
- If two stories are genuinely separate but feel similar, emphasize the distinction in the "Why it matters" line.

### Narrative frame or takeaway feels generic

- The narrative layer only works when there's a real thread. If the stories are truly unrelated, skip the connecting theme and instead tease what each story sets up for the near term.
- A good test: if the narrative frame could apply to any week's news, it's too vague. Make it specific to today's stories.

### Skill triggers when it shouldn't

The description is intentionally broad to catch natural phrasing. If it fires too often:

1. Add `disable-model-invocation: true` to frontmatter — you'll need to invoke it manually with `/tech-update`.
2. Or narrow the description to require more specific phrasing.

### Skill doesn't trigger when it should

1. Try more explicit phrasing: "give me a tech update" or "what's the latest in AI."
2. Invoke directly with `/tech-update` to confirm the skill loads.
3. Run `/context` to confirm the skill's description is within the character budget.

---

## Extending the Skill

### Adding a `scripts/` helper

If you want to automate source fetching or post-process the output (e.g., save to a file, send to Slack), add a script to `.claude/skills/tech-update/scripts/` and call it from SKILL.md using dynamic context injection or a Bash step.

Example: auto-save the briefing to a dated file:

```yaml
## After formatting output
Save the full briefing to `notes/tech-updates/YYYY-MM-DD.md` using today's date.
```

### Running as a Subagent

To keep the web search out of your main conversation context, add `context: fork` and `agent: general-purpose` to the frontmatter. The skill runs in isolation and returns only the final briefing.

```yaml
---
name: tech-update
description: Use when someone asks for tech news ...
context: fork
agent: general-purpose
allowed-tools: WebSearch, WebFetch
---
```

Trade-off: with `context: fork`, you lose the ability to ask follow-up questions in the same thread. Use it when you want a clean, one-shot briefing.
