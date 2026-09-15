---
name: yt-ideas
description: Use when someone asks for anime research, what's trending in anime right now, or a deeper dive on a specific anime topic.
argument-hint: [deep-dive <topic> — optional]
allowed-tools: Read, Write, WebSearch, WebFetch
disable-model-invocation: true
---

For the complete reference on search strategy, the volume-vs-saturation distinction, and troubleshooting, see [reference.md](reference.md).

## What This Skill Does

A broad anime research digest — not scoped to any one channel, format, or niche. Scans what's actually happening across anime right now (new releases, controversies, announcements, viral moments — excluding Shorts) and produces plain research write-ups: what's happening, the real context behind it, and how much genuine attention it has versus how crowded it already is on YouTube. No invented titles, no "why this fits your channel," no verdict on what to make — just the research, so you can decide what to do with it yourself. A `deep-dive` step expands one topic further when you want more than the digest-level summary.

**Shorts are explicitly out of scope everywhere** — research excludes Shorts-format content and Shorts-driven trends throughout.

## Modes

The first token of `$ARGUMENTS` selects the mode. If it isn't `deep-dive`, treat the whole run as Mode 1.

| Argument | Mode |
|----------|------|
| _(empty)_ | Research digest — 4–6 well-corroborated anime topics |
| `deep-dive <topic>` | Expand one topic (from this run or a past one) with more research |

---

## Mode 1: Research Digest _(default)_

1. Read `content/anime-research.md` if it exists — note what's already been covered recently, so a topic doesn't get a near-identical write-up two runs in a row without something new to add. This is a soft check, not a hard exclusion rule — unlike a video-idea log, re-covering a still-developing story with new details is fine and expected.

2. Search across anime happenings using **all** of the angle groups below as **separate searches** — not one combined query per group, so no single angle (or single franchise) crowds out the others. Exclude Shorts-specific content from every angle:

   **General angles:**
   - New episode/season releases and premieres airing this week
   - Community controversies, discourse, or "hot takes" circulating
   - Announcements — new seasons, adaptations, casting, studio news
   - Viral clips/moments (a scene, a reaction, a meme) gaining traction — long-form discussion of a moment counts; a Shorts trend built around it does not

   **Romance anime angle (always run, not optional — this is a standing content preference, weight it accordingly):**
   - What's happening specifically in romance anime this season — new releases, discourse, standout episodes, adaptation news
   - **Separately and explicitly, check what's currently airing** — not just new/upcoming titles. A "new release OR season [upcoming season]" style query structurally excludes shows that premiered a cour or two ago and are still mid-run; pull the current season's full romance lineup (see the smaller-anime angle's seasonal-chart step) and check episode-level reaction activity per title, not just what's freshly announced.
   - For the YouTube-side check, search *by candidate title* (`"[title]" reaction YouTube episode`) rather than only generic "romance anime trending YouTube" phrasing — the generic phrasing tends to return ranked listicle videos ("Top 10 Trending Romance Anime"), not the actual per-show reaction landscape, and will systematically miss a title that has real, consistent episode-by-episode reaction coverage but hasn't been packaged into a ranking video yet. Also note: this is a web-search proxy for YouTube activity, not YouTube's own trending/analytics data — treat a real pattern of dated per-episode reaction videos (multiple channels, recent uploads) as the actual signal, not the word "trending" appearing in a result.

   **Smaller/less-mainstream anime angle (always run, not optional):**
   - Not "underrated" in the broadest sense — target the specific visibility tier Daemons of the Shadow Realm represents: real critical reception and/or pedigree (award nods, a notable creator/studio, solid review scores), genuine but modest recurring YouTube reaction coverage, yet never breaking into the same tier of general discussion as that season's tentpole franchises. See reference.md's "Calibrating Volume for Smaller/Underrated Titles" section for exactly what does and doesn't count at that tier.
   - Actively search with "underrated," "hidden gem," "overlooked" framing, not just generic anime news, since plain searches default to whatever the biggest franchises are — but then filter what turns up against the Daemons-tier bar rather than including anything that matches the search words alone
   - Consider checking a full seasonal chart (not just a top-10 list) for titles at that tier, rather than only what press coverage already amplified

3. General-angle searches on their own will structurally over-represent the two or three biggest franchises airing that season (they simply generate the most press) — the romance and smaller-anime angles exist specifically to counteract that default.
   - **At least 1 of the final selected topics should be a romance-anime topic every run.** This floor belongs to romance specifically, not shared with the smaller-anime angle — it reflects a standing preference for romance content, so romance should be represented every digest, not just often.
   - **Separately, when a genuine Daemons-tier candidate turns up, prioritize it over an additional mainstream-franchise topic.** This isn't a forced-every-run floor the way romance is — a real Daemons-tier title (see reference.md for the exact bar) won't necessarily surface every single week, and forcing in something too obscure (no real footprint) or something that's actually mainstream just to fill a slot is worse than skipping it that week.
   - If a run's general-angle searches turn up 4 tentpole-franchise stories and nothing else, that's a signal to search harder on the other two angles before finalizing, not a reason to fill the digest with mainstream news alone.

4. For every candidate topic, use WebFetch on the actual source article(s) before writing it up — this is mandatory, same standard as every other research skill in this repo. Retry with an alternate outlet on failure before falling back to a search snippet; never build a topic's write-up entirely from an unfetched snippet.

5. For every candidate topic, assess two distinct signals — see reference.md for the full bar:
   - **Volume of interest:** corroborated by more than one independent source, or a source citing a concrete engagement signal (view counts, a trending ranking, a large discussion thread)? A single obscure post is weak, not confirmed. For smaller/underrated titles specifically, hold this to a lighter standard — see reference.md's note on calibrating volume for lower-visibility anime, since a niche title will never rack up press coverage the way a tentpole franchise does.
   - **YouTube saturation:** separately, how much long-form YouTube coverage already exists on this exact angle. This is informational, not a filter on whether to include the topic — a heavily-covered topic can still be worth knowing about, it's just useful to know it's crowded. Smaller titles will typically show low saturation almost by definition — that's worth noting explicitly as a point in their favor, not just a neutral fact.

6. Select 4–6 topics that are genuinely well-corroborated — never pad to hit 6 on a quiet week; 2 solid topics beat 6 thin ones. Apply the romance floor and the Daemons-tier smaller-anime consideration from Step 3 when selecting, not just when searching — the romance floor is non-negotiable, the smaller-anime one is opportunistic.

7. Format using the template below, display the **complete, full write-up in the chat response itself** — every topic, both full paragraphs, the Volume/Saturation lines, and sources, word-for-word identical to what gets saved — then append that same content to `content/anime-research.md` (create `content/` and the file from the template if this is the first run). Never display a trimmed, condensed, or summarized version in chat while saving the full version to the file — the file is a record, not the only place to read the research; the chat response must stand on its own.

**Output format:**

```markdown
## 📚 Anime Research — [Month Day, Year]

### [Topic name — descriptive, not a pitch]

[First paragraph: 2–4 sentences of the core facts — what's happening, key dates/names/numbers.]

[Second paragraph: context — why this is happening now, background, who's involved.]

**Volume:** [the concrete evidence behind the interest claim]
**Saturation:** [how much existing long-form YouTube coverage exists on this exact angle, with reasoning]

🔗 [Source Name](url), [Source Name](url)

### [Next topic]
...
```

---

## Mode 2: Deep-Dive (`deep-dive <topic>`)

1. Read `content/anime-research.md`. Match `$ARGUMENTS` (everything after `deep-dive`) against a topic from the most recent run or an earlier logged entry. If more than one plausibly matches, or nothing matches closely, ask the user to confirm rather than guessing — or treat it as a fresh topic if it's genuinely new.

2. Research further via WebSearch/WebFetch — more sources, more angles (different community reactions, any official statements, historical context if relevant), more depth than the digest-level write-up.

3. Display the complete, full expanded write-up in the chat response itself — same rule as Mode 1 Step 7, word-for-word identical to what gets saved, never a condensed version — then append it under the matching topic's entry in `content/anime-research.md`.

**Output format:**

```markdown
### Deep-dive: [Topic name] — [Month Day, Year]

[Expanded research — as many paragraphs as the topic actually supports, still factual, no pitch or verdict.]

🔗 [Additional sources beyond the original digest entry]
```

---

## File Template

**`content/anime-research.md`:**

```markdown
# 📚 Anime Research Log

[Entries appended here chronologically, oldest first — same append-only convention as journal/.]
```

## Notes

- `content/` is gitignored — never commit or suggest committing anything in it. If `content/` doesn't exist, create it the first time this skill runs.
- Never fabricate a "trending" claim, a source, a volume signal, or a saturation assessment. If genuine material is thin this week, surface fewer than 6 topics rather than padding — say so plainly.
- No channel-fit filtering, no invented titles, no "why you should make this" reasoning, no thumbnail or script drafting — this skill is research only. `content/channel-profile.md` exists from a prior version of this skill but is not read or used here.
- Shorts are out of scope everywhere — research excludes Shorts-format content and Shorts-driven trends.
- No tone filtering: surface a genuinely trending controversy or hot take as readily as a lighter topic — the only filters are real volume and genuine WebFetch-backed sourcing.
- `content/anime-research.md` is append-only, same rule as `journal/YYYY-MM.md` — never edit or remove a past entry, only add to the file.
