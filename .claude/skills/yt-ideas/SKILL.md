---
name: yt-ideas
description: Use when someone asks for YouTube video ideas, what's trending in anime right now, what to make a video about, or to develop a hook and outline for a specific video idea.
argument-hint: [develop <idea or topic> — optional]
allowed-tools: Read, Write, WebSearch, WebFetch
disable-model-invocation: true
---

For the complete reference on search strategy, volume-signal criteria, and troubleshooting, see [reference.md](reference.md).

## What This Skill Does

Research-driven anime YouTube content ideation for [GunnnarZ](https://www.youtube.com/@gunnnarz). Scans current anime buzz (new releases, controversies, viral moments), cross-references your own channel's style and what similar-sized anime channels are covering, and surfaces ranked video ideas you haven't already covered. A separate `develop` step drafts an opening hook + outline for one chosen idea. Everything is logged to `content/yt-ideas.md` (gitignored) so ideas don't repeat and drafts aren't lost between runs.

No thumbnail generation — that's handled separately by hand.

## Modes

The first token of `$ARGUMENTS` selects the mode. If it isn't `develop`, treat the whole run as Mode 1.

| Argument | Mode |
|----------|------|
| _(empty)_ | Surface 3–5 new ranked video ideas |
| `develop <idea or topic>` | Draft a hook + outline for that idea |

---

## Mode 1: Surface Ideas _(default)_

1. Read `content/yt-ideas.md` if it exists. Note every idea already suggested and every topic already developed — these are off-limits for a fresh suggestion unless genuinely new information has emerged (say so explicitly if that's why you're resurfacing one).

2. Research the channel via WebSearch — recent video titles, recurring formats/series, tone and voice. **Do not attempt WebFetch on youtube.com/@gunnnarz or any YouTube watch/channel page directly — YouTube is JS-rendered and returns only nav/footer boilerplate.** Search for `"GunnnarZ" YouTube anime` and similar queries instead, and rely on search-result snippets, any press/wiki coverage, and video titles that surface.

3. Search broadly for current anime buzz. Run these as **separate searches**, not one combined query, so no single angle crowds out the others:
   - New episode/season releases and premieres airing this week
   - Community controversies, discourse, or "hot takes" circulating
   - Announcements — new seasons, adaptations, casting, studio news
   - Viral clips/moments (a scene, a reaction, a meme) gaining traction

4. For every candidate topic, gauge volume of interest before it earns a spot — see reference.md for the full bar, but at minimum: is it corroborated by more than one independent source, or does a source cite an explicit engagement signal (view counts, a trending ranking, a high-reply thread)? A topic surfaced by exactly one obscure post is a weak candidate, not a confirmed trend — say so if you include it anyway for a specific reason.

5. Search for similar-sized anime commentary/reaction channels covering related content — discover these fresh each run rather than maintaining a fixed list. Note what topics they're currently covering or what looks like it's performing well for them.

6. Cross-check every remaining candidate against the channel's past videos (step 2) and the logged ideas/drafts (step 1). Drop duplicates, or flag clearly if you're intentionally resurfacing one.

7. Select 3–5 ideas that are genuinely well-supported — never pad to hit 5 on a quiet week; 2 strong ideas beat 5 weak ones. Rank by apparent interest volume, strongest first.

8. Format using the template below, display it, then append the same content to `content/yt-ideas.md` (create `content/` and the file from the template if this is the first run).

**Output format:**

```markdown
## 🎬 Video Ideas — [Month Day, Year]

1. **[Idea title]**
   - **Trend:** [what's actually happening, with specifics — not vague]
   - **Why it fits:** [tie to the channel's established style/past videos]
   - **Buzz:** [the concrete evidence behind the volume claim]
   🔗 [Source Name](url), [Source Name](url)

2. **[Next idea]**
   ...

_Competitor angle: [1–2 sentences on what similar channels are doing with related topics right now, if it's relevant to any idea above — omit this line if nothing notable turned up.]_
```

---

## Mode 2: Develop (`develop <idea or topic>`)

1. Read `content/yt-ideas.md`. Match `$ARGUMENTS` (everything after `develop`) against an idea from the most recent run or an earlier logged entry. If more than one plausibly matches, or nothing matches closely, ask the user to confirm which idea they mean rather than guessing.

2. Pull the topic's key facts — reuse what was already researched if it's from this run; re-search via WebSearch if it's an older logged idea and the details might be stale.

3. Draft:
   - **Hook (0:00–0:30):** 3–5 sentences that open by stating the take or question directly, written in the channel's established voice (direct, opinionated — matched to what step 2 of Mode 1 found about the channel's tone).
   - **Outline:** a bullet list of the sections/beats the rest of the video should hit, in order. Structure only — not full dialogue.

4. Display the draft, then append it under the matching idea's entry in `content/yt-ideas.md`.

**Output format:**

```markdown
### Developed: [Idea title] — [Month Day, Year]

**Hook (0:00–0:30):**
[drafted hook]

**Outline:**
- [beat]
- [beat]
...
```

---

## File Template

**`content/yt-ideas.md`:**

```markdown
# 🎬 YouTube Video Ideas — GunnnarZ

[Entries appended here chronologically, oldest first — same append-only convention as journal/.]
```

## Notes

- `content/` is gitignored — never commit or suggest committing this file. If `content/` doesn't exist, create it the first time this skill runs.
- Never fabricate a "trending" claim, a source, or a buzz signal. If genuine buzz is thin this week, surface fewer than 5 ideas rather than padding — say so plainly.
- No tone filtering: surface a genuinely trending controversy or hot take as readily as a lighter topic. Don't pre-filter for "safety" — the only filter is whether the buzz is real and whether it fits the channel.
- Thumbnail concepts are explicitly out of scope for this skill — never generate them even if asked; that's handled by hand.
- Competitor channels are discovered fresh each run, not maintained as a fixed watchlist.
- `content/yt-ideas.md` is append-only, same rule as `journal/YYYY-MM.md` — never edit or remove a past entry, only add to the file.
- Keep headlines/idea titles punchy and specific: "Why [Character]'s Season 3 Turn Is Splitting the Fandom," not "Thoughts on Season 3."
