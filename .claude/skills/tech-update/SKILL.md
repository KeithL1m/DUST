---
name: tech-update
description: Use when someone asks for tech news, the latest AI updates, what's happening in the tech industry, or updates on a specific company or topic.
argument-hint: [topic or company — optional]
allowed-tools: WebSearch, WebFetch
---

For the complete reference on argument usage, search strategy, source priority, output format details, customization, and troubleshooting, see [reference.md](reference.md).

## What This Skill Does

Fetches and summarizes the latest news in tech, AI, and the broader industry. Accepts an optional topic or company name to narrow the focus; defaults to a general roundup.

## Steps

1. Check `$ARGUMENTS`. If provided, use it as the focus (e.g., "OpenAI", "AI hardware", "Apple"). If empty, do a general tech news roundup.

2. Search for recent stories (past 48 hours preferred; past week if recent results are thin):
   - If focused: search for `$ARGUMENTS` + "news" + current date range
   - If general: search across AI, major tech companies (including Chinese firms like Baidu, Alibaba, Tencent, Huawei, ByteDance, DeepSeek), hardware, and industry moves

3. Select 5–8 stories that are genuinely noteworthy — prioritize announcements, product releases, funding rounds, regulatory news, and research breakthroughs. Skip opinion pieces and minor updates.

4. For every selected story, without exception, use WebFetch on the article URL to get: a richer description of what happened, any relevant data points or quotes, and the article's main image URL (look for `og:image` in the page metadata or the first prominent `<img>` tag). This step is mandatory, not optional — do not move on to composing a story from the WebSearch summary alone without first attempting the fetch. If the fetch fails (403, paywall, empty page), retry with at least one alternate outlet covering the same story before falling back to the search snippet. A story built entirely from search snippets with no WebFetch attempt at all should never happen; a story that falls back after a genuine retry failure should be rare, not routine.

5. Format the output using the template below.

## Output Format

```
---

# 🗞 Tech Briefing — [Month Day, Year]

[2–3 sentence narrative framing the day's tech landscape — the theme or tension running through the stories. Synthesize, don't list.]

---

## [Story Headline — present tense, active voice]

**[1–2 sentence summary: the core fact of what happened, for readers who skim.]**

[First chunk: 2 sentences of the core facts — numbers, dates, product names, quotes.]

[Second chunk: 2–3 sentences compressing context and background — why now, how it fits the competitive landscape. Same story, split for breathing room, not extra material.]

**Why it matters:** [1 sentence. The single sharpest forward-looking implication — what changes, who wins or loses, or what to watch. Pick the one that matters most.]

🔗 [Source Name](article-url)

---

## [Next Story Headline]

...repeat for each story...

---

### 🔍 The Big Picture

[First paragraph: 3 sentences identifying the thread connecting all the stories.]

[Second paragraph: 3 sentences on what the pattern signals about where the industry is heading and what to watch closely in the coming week.]

---
```

**Summary line rules:**
- 1–2 sentences max. The core fact only — who did what.
- No context, no interpretation. That's what the deep-dive paragraphs are for.

**Deep-dive paragraph rules:**
- Split into two short chunks with a blank line between them, purely for visual breathing room — not the old full two-paragraph structure. Total content across both chunks is 4–5 sentences combined, not 4–5 sentences each.
- First chunk: 2 sentences — facts, details, data points, quotes from the article.
- Second chunk: 2–3 sentences — context and background, compressed, not a full second paragraph.
- Every story needs at least 3 concrete data points across the two chunks (a figure, a date, a product name, a quote, etc.) — splitting for readability must not come at the cost of specifics, or become an excuse to add length back.
- Write clearly enough that someone outside tech can follow along.
- No editorializing — save interpretation for "Why it matters."

**Why it matters rules:**
- Exactly 1 sentence. The single sharpest forward-looking implication — pick the one angle (what changes, who wins/loses, or what to watch) that's most load-bearing for this story and cut the rest.

## Notes

- If `$ARGUMENTS` is a company name with no recent news, say so and offer to broaden the search.
- Don't fabricate stories or data points. If the article fetch genuinely fails after a retry with an alternate outlet, use only what the search result provided — but the fetch (and retry) must actually be attempted first, every time, for every story. Skipping straight from search results to prose without trying WebFetch at all is the main way stories come out thin or stale, even when the format itself is short.
- Avoid duplicating the same story from multiple sources — pick the best source and move on.
- Keep headlines in present tense, active voice: "OpenAI Ships GPT-5" not "GPT-5 Was Released."
