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

4. For each selected story, use WebFetch on the article URL to get: a richer description of what happened, any relevant data points or quotes, and the article's main image URL (look for `og:image` in the page metadata or the first prominent `<img>` tag).

5. Format the output using the template below.

## Output Format

```
---

# 🗞 Tech Briefing — [Month Day, Year]

[2–3 sentence narrative framing the day's tech landscape — the theme or tension running through the stories. Synthesize, don't list.]

---

## [Story Headline — present tense, active voice]

> [1–2 sentence summary: the core fact of what happened, for readers who skim.]

[First deep-dive paragraph: 3–4 sentences covering what happened, who the key players are, and the specific details — numbers, dates, product names, quotes.]

[Second deep-dive paragraph: 3–4 sentences covering context and background — why this is happening now, what led up to it, and how it fits the broader competitive landscape.]

**Why it matters:** [3–4 sentences. Forward-looking analysis: what this changes, who wins or loses, what it signals about where the industry is heading, and what to watch next.]

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
- Split into two separate paragraphs with a blank line between them — never one dense block.
- First paragraph: facts, details, data points, quotes from the article.
- Second paragraph: context, background, competitive landscape.
- Write clearly enough that someone outside tech can follow along.
- No editorializing — save interpretation for "Why it matters."

**Why it matters rules:**
- 3–4 sentences. Focus on implication and forward-looking significance.
- Answer: What does this change? Who wins or loses? What should someone watch next?

## Notes

- If `$ARGUMENTS` is a company name with no recent news, say so and offer to broaden the search.
- Don't fabricate stories or data points. If the article fetch fails, use only what the search result provided.
- Avoid duplicating the same story from multiple sources — pick the best source and move on.
- Keep headlines in present tense, active voice: "OpenAI Ships GPT-5" not "GPT-5 Was Released."
