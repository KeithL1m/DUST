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

4. Format the output using the template below.

## Output Format

Start with a 1–2 sentence narrative framing the day's tech landscape (the "headline mood" — e.g., "AI infrastructure spending is dominating headlines this week as...").

Then list each story as a bullet:

**[Story Headline]**
[1-sentence summary of what happened.] **Why it matters:** [1 sentence on the significance or implication.]

Close with a 2–3 sentence narrative takeaway: the thread connecting the stories, or what to watch next.

## Notes

- If `$ARGUMENTS` is a company name with no recent news, say so and offer to broaden the search.
- Don't fabricate stories. If search results are sparse, return what's available and note the limitation.
- Avoid duplicating the same story from multiple sources — pick the best source and move on.
- Keep the total output scannable — this is a briefing, not a deep dive.
