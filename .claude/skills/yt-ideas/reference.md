# YT Ideas Reference

Complete reference for the yt-ideas skill (an anime research digest — see SKILL.md for the full scope). Covers search strategy, the volume-vs-saturation distinction, source priority, output format details, customization, and troubleshooting.

---

## Argument Reference

`$ARGUMENTS` is optional. The first token decides the mode.

| Input | Behavior |
|-------|----------|
| _(empty)_ | Research digest — 4–6 well-corroborated anime topics |
| `deep-dive <topic>` | Expand one topic with more research |

**Examples:**
```
/yt-ideas                          → Fresh research digest
/yt-ideas deep-dive Ranma 1/2      → Expanded research on that topic
```

---

## Search Strategy

### Date Range Logic

1. **Past 24–48 hours** for viral clips/moments and discourse — these move fast and go stale within days.
2. **Past week** for episode/season releases and announcements — acceptable to look slightly further back since a release's discussion tail lasts longer than a single clip's.
3. **Always disclose** if you had to widen beyond 48 hours to find enough material.

### Constructing Search Queries

Run separate searches per angle — a single broad query lets one loud story crowd out everything else. Add "-shorts" or similar exclusion language where a query is prone to surfacing Shorts-format results specifically (viral-moment searches are the most likely offender).

| Angle | Example query |
|-------|---------------|
| New releases | `anime new episode OR season premiere this week 2026` |
| Controversy/discourse | `anime controversy OR debate OR discourse trending 2026` |
| Announcements | `anime announcement OR adaptation OR casting news 2026` |
| Viral moments (long-form only) | `anime viral clip OR scene OR reaction trending 2026 -shorts` |
| Romance anime — new/upcoming | `romance anime new release OR discourse OR season [current season] 2026` — run every time, not conditionally |
| Romance anime — currently airing | Pull the current season's full romance lineup (seasonal chart, not just what's newly announced), then check each candidate individually — the "new release" query above will not surface a title that premiered a cour ago and is still mid-run |
| Romance anime — YouTube-specific, per-title | `"[candidate title]" reaction YouTube episode` for each currently-airing candidate — not a generic "romance anime trending YouTube" query, which returns ranked listicle videos instead of a specific show's actual reaction landscape. A real signal looks like: multiple channels, recent upload dates, episode numbers climbing in step with the show's air schedule — not the word "trending" appearing in a page title. This is what feeds the mandatory romance floor in SKILL.md Step 3. |
| Smaller/underrated anime | `underrated OR hidden gem OR overlooked anime 2026` and `anime seasonal chart [current season] 2026` (to see the full lineup, not just what's already popular) — run every time, not conditionally; filter results against the Daemons-tier bar below before treating anything as a candidate |
| Saturation check | `[topic] anime reaction OR review YouTube` — used to gauge how much long-form coverage already exists |

**Why the romance and smaller-anime angles are separate, mandatory queries, not folded into the general ones:** generic "anime news" searches are structurally biased toward whatever already has the most press — in practice, that means 2-3 tentpole shonen franchises dominate almost every general search result. Romance anime and smaller titles don't compete for the same headlines, so they need their own dedicated search to surface at all. Skipping these two angles (or treating them as optional) reproduces the exact mainstream-only bias this section exists to counteract.

**Why romance also gets a YouTube-specific query, not just a press query:** press coverage of a romance title and that title actually getting reaction/discussion traction on YouTube are different signals — a title can be well-covered in anime press without much YouTube-side pull, or vice versa. Since romance is a standing content preference (not just one angle among equals), the digest should be tracking the YouTube-side signal directly rather than inferring it from press coverage alone.

### Source Priority

**Tier 1 — Primary/high-signal:**
- Official studio/publisher announcements, streaming platform news pages (Crunchyroll, official licensor sites)
- Direct engagement data cited by a source (view counts, trending chart position, reply/upvote counts)

**Tier 2 — Established anime press:**
- Anime News Network, Crunchyroll News, IGN/Polygon anime coverage, major outlet entertainment sections

**Tier 3 — Community signal (context, not sole confirmation):**
- Reddit threads, X/Twitter discussion, forum posts — useful for confirming discourse is real, but a single Tier 3 post alone isn't enough to call something "trending"

**Avoid:**
- Pure clickbait aggregator sites with no original reporting
- A single source with no corroboration anywhere else — that's a hunch, not a trend

---

## Volume of Interest vs. YouTube Saturation

These are two different questions, tracked separately for every topic.

### Volume of Interest

What actually counts as "has real attention" versus "one person's take":

**Clears the bar:**
- Multiple independent sources (2+ outlets, or an outlet plus visible community discussion) covering the same topic within the same window
- A source that cites a concrete signal: a trending chart position, a specific view/engagement count, a notably large or fast-growing discussion thread
- A topic several unrelated commentary channels are covering around the same time

**Doesn't clear the bar on its own:**
- A single blog post or forum thread with no corroboration
- A topic that's evergreen/always-discussed rather than freshly spiking, with no real "why now"
- Something that only your own search phrasing surfaced, with no independent trace of it elsewhere

### YouTube Saturation

A separate question: **how many long-form YouTube videos already cover this exact angle?** This skill reports saturation as information, not as a filter — unlike an idea-generation tool, a research digest doesn't need to avoid "crowded" topics; knowing something is already heavily covered is itself useful context.

- **Low saturation:** few or no long-form videos found on this specific angle yet
- **Medium saturation:** some coverage exists, but plenty of unexplored angles remain
- **High saturation:** many videos already cover the obvious version of this story

**When in doubt on either axis:** say so plainly — e.g., "Volume: moderate — one detailed Reddit thread (400+ comments) but no press coverage yet. Saturation: high — a dozen+ reaction videos already exist." Never round a weak signal up to sound more confident than it is, in either direction.

### Calibrating Volume for Smaller/Underrated Titles

The "2+ independent sources" bar above was written with mainstream franchises in mind, where that bar is easy to clear. A genuinely underrated or niche anime will often never get that kind of press coverage no matter how good it is or how devoted its (smaller) audience is — holding it to the exact same bar as a One Piece story would systematically exclude every smaller title, defeating the point of searching for them at all.

For a smaller-anime candidate, this counts as real volume even without 2+ press outlets:
- One clearly substantive source (a detailed review, a seasonal-anime roundup that specifically calls it out, a sizeable dedicated discussion thread) plus a real MyAnimeList/seasonal-chart presence
- A consistent pattern across multiple "underrated"/"hidden gem" roundup articles, even if each individual mention is brief

Still be honest about the difference in scale — a smaller title's "Volume: moderate, one detailed review plus consistent hidden-gem-list mentions" is not the same claim as a tentpole franchise's "Volume: high, five independent outlets within 48 hours," and the write-up should read that way rather than borrowing mainstream-scale confidence language for a niche title's genuinely smaller footprint.

### What "Daemons-Tier" Means, Specifically

Daemons of the Shadow Realm (see the Worked Example below) is the calibration anchor for the smaller/less-mainstream angle — not "any underrated anime," a specific visibility band. A candidate is genuinely at that tier when it shows **most or all** of:

- **Real critical/pedigree signal:** a legitimate positive review (not just a fan tweet), and/or an award nomination or win, and/or a notable creator/studio attached (an established mangaka, a recognized studio, a director with a track record)
- **A real but modest recurring YouTube footprint:** more than zero — a small set of channels have covered it consistently over its run — but not remotely close to a tentpole franchise's reaction ecosystem
- **Never broke into tentpole-level general discussion:** it doesn't show up in the same "everyone's talking about this" conversations as that season's 2-3 biggest franchises, despite the pedigree above

**Two failure modes to actively check against, not just "is it popular":**
- **Too obscure / already forgotten:** a title with no real critical footprint, no recurring coverage, and nothing beyond a single passing mention in a roundup list — this doesn't clear the bar either; it's not "smaller," it's just thin. (Twilight Out of Focus and Ikoku Nikki were rejected on a prior run for a related reason — already-concluded, not currently live — but a title with genuinely no footprint at all should be rejected on this ground.)
- **Secretly mainstream:** a title that shows up in mainstream roundups, has a large multi-channel reaction ecosystem, or is a sequel/spinoff of an already-huge franchise doesn't qualify just because a search happened to surface it under "hidden gem" framing — check saturation before finalizing a smaller-anime candidate, not just volume.

If nothing found this run genuinely sits in that band, don't force a substitute from either failure mode into the digest — see SKILL.md Step 3's note that this angle is opportunistic, not a forced-every-run floor the way romance is.

---

## Output Format Reference

See SKILL.md for the full templates. Key rules:

**Topic headers:** Descriptive, factual — name the actual show/character/event. Not a pitch, not a question, not a hook. "Ranma 1/2 Remake Premieres October 5" not "Is Ranma 1/2's Remake About to Change Everything?"

**First paragraph:** State the actual facts with specifics (what happened, when, who's involved) — not vague scene-setting.

**Second paragraph:** Context and background — why this is happening now, what led up to it.

**Volume line:** Must name the actual evidence (source count, an engagement number, cross-channel coverage) — never just assert "high interest" with nothing behind it.

**Saturation line:** Must state low/medium/high with the reasoning (how many existing videos, roughly) — never omit this even when saturation is low.

**No verdict, no pitch, no channel-fit reasoning** anywhere in the output — this is the core difference from the skill's earlier version. If you catch yourself writing "this would make a great video because..." or matching a title to some assumed voice, stop — that's not what this skill produces anymore.

### Worked Example (user-confirmed as the target quality bar)

These two entries are from the September 14, 2026 run that the user explicitly confirmed as "looks really good" — use them as the concrete calibration point whenever the abstract rules above leave ambiguity about tone, density, or how to handle a mixed/nuanced signal.

```markdown
### Blue Box Season 2 Confirmed for October 4 on Netflix

Blue Box's second season premieres October 4 worldwide on Netflix, with a new trailer revealing theme songs — opening "Anata no Hana no Iro" by aiko, ending "blue in" by Elsewhere Kikou. Director Daisuke Sako takes over from the first season's director, and animation studio Electric Circus replaces Telecom Animation Film, while the original voice cast returns alongside new addition Yoshino Aoyama.

The source manga concluded its five-year run earlier in 2026, so this season has a clear path to adapt the story's ending — with no new manga chapters coming, this is now the only new content for the franchise, which is why manga readers online are specifically flagging it as "anime-only fans aren't remotely prepared for what's ahead."

**Volume:** High — official confirmation covered independently by ComicBook, Screen Rant, and Netflix's own channels, plus genuine organic fan discussion (not just PR amplification) from manga readers anticipating the adaptation.
**Saturation:** Low — the season hasn't aired yet, so existing YouTube coverage is limited to official trailer reactions; no episode content exists to react to.

🔗 [ComicBook.com](https://comicbook.com/anime/news/blue-box-season-2-confirms-netflix-release-date-with-new-trailer-watch/)
```

```markdown
### Daemons of the Shadow Realm Wrapped September 12 — a Genuine Hidden Gem From FMA's Creator

Hiromu Arakawa's (Fullmetal Alchemist) follow-up manga got a 24-episode, two-cour anime adaptation from Bones (the studio behind FMA: Brotherhood), directed by Masahiro Andō — it concluded its run September 12. Critics have been consistently positive throughout: an 8/10 "GREAT" review highlighted a mid-series twist compared favorably to M. Night Shyamalan's best, and the source manga has sold over 6 million copies and won the Tsutaya Comic Award grand prize.

Despite that pedigree, this hasn't broken into the same tier of mainstream discussion as this season's biggest franchise releases — it's a clear example of a well-reviewed, well-supported title that's flying under more casual fans' radar simply by not being an established mega-franchise.

**Volume:** Moderate-high for a non-tentpole title — multiple positive reviews (JoBlo, FandomWire), strong manga sales/award pedigree, and a real (if smaller) recurring reaction-channel audience across its run — a stronger footprint than most "hidden gem" candidates typically show.
**Saturation:** Medium — several channels have covered it consistently since April, but it's a noticeably smaller reaction ecosystem than a tentpole franchise like Bleach or One Piece, not a crowded space.

🔗 [JoBlo](https://www.joblo.com/daemons-of-the-shadow-realm-review/), [Wikipedia](https://en.wikipedia.org/wiki/Daemons_of_the_Shadow_Realm)
```

What makes these the target bar, specifically:
- Every claim traces to a named, fetched source — nothing is asserted without evidence.
- The volume and saturation lines are genuinely distinct assessments, not restatements of each other, and both name concrete reasoning rather than just a confidence word.
- The Daemons entry shows the smaller-title volume calibration in action: "moderate-high for a non-tentpole title" is honest about scale rather than borrowing mainstream-confidence language, per the calibration section above.
- Both stay factual and descriptive throughout — no line pitches the topic or implies what should be made from it.

That same run also stopped at 3 topics instead of padding to 4-6, explicitly noting in the file that the remaining general-angle candidates were repeats of an already-logged story — this is the padding rule (Step 6) working as intended, not a shortfall.

---

## Customization

### Adjusting Topic Count

Change "4–6" in SKILL.md Mode 1 Step 6 if that feels like too many to read at once, or too few on a busy week.

### Adjusting the Romance Floor / Smaller-Anime Bar

Romance and smaller-anime now have separate treatment in SKILL.md Mode 1 Step 3: romance has a hard floor (at least 1 topic every run, reflecting a standing content preference), while smaller-anime is opportunistic and gated by the Daemons-tier bar in this file rather than a forced count. Raise the romance floor above 1 in SKILL.md if it's still getting crowded out; lower it only if it's forcing in a genuinely weak romance topic on a thin week (never pad just to hit it — see the padding rule in Step 6). For smaller-anime, adjust the Daemons-tier definition above rather than adding a count-based floor — if that angle is turning up nothing for many runs in a row, the bar may be too strict; if it's letting in too-obscure or secretly-mainstream candidates, tighten the two failure-mode checks instead.

### Widening Beyond Anime

The focus is currently hardcoded to anime. If that changes, update SKILL.md's "What This Skill Does" section and the search queries in this file's Search Strategy table accordingly.

### Scheduling as a Recurring Check

`/schedule` can run `/yt-ideas` on a recurring basis (e.g., weekly) the same way tech-update and morning-coffee can. `deep-dive` requires a specific topic and isn't a good candidate for unattended scheduling — only schedule the default digest mode.

### Reintroducing Channel-Fit Filtering

If channel-specific filtering is wanted again later, `content/channel-profile.md` (from the skill's earlier version) still exists on disk with real performance data and could be reintroduced as an optional weighting layer — but that's a deliberate, separate decision, not the current default. Don't reintroduce it silently; confirm with the user first, since removing it was an explicit, considered choice.

---

## Troubleshooting

### WebFetch on a YouTube URL returns nothing useful

Expected — YouTube channel and video pages are JS-rendered and WebFetch only sees the static footer/nav shell. This mostly doesn't come up anymore since this version of the skill doesn't do channel research, but it still applies if a source link happens to be a YouTube URL during general topic research.

### Research feels repetitive across runs

Check that `content/anime-research.md` is actually being read in Mode 1 Step 1 — if skipped, near-duplicate write-ups won't get caught. Remember this is a soft check, not a hard exclusion — a still-developing story getting a fresh write-up with new facts is expected and fine.

### A "Volume" or "Saturation" claim doesn't hold up on a second look

Same failure mode documented in morning-coffee and tech-update's reference docs: a claim not traced to a real, checkable source. Every "Volume" and "Saturation" line must name real evidence — if you can't point to the specific source(s) behind either claim, downgrade the confidence language rather than present it with false certainty.

### Too many/too few topics clearing the bar

If most weeks produce fewer than 3 solid topics, the volume-of-interest bar may be too strict — consider loosening the "2+ independent sources" requirement to allow one strong Tier 2 source alone. If every run maxes out at 6 with room to spare, consider raising the count in SKILL.md Mode 1 Step 6.

### The digest is still all mainstream franchises despite the romance/smaller-anime angles

Check that those two angles are actually being run as their own separate searches (SKILL.md Mode 1 Step 2), not skipped or folded into the general queries — folding them in reproduces the exact bias they're meant to fix, since a combined query still ranks by overall coverage volume. Also check that the volume bar for smaller titles is being calibrated per the "Calibrating Volume for Smaller/Underrated Titles" and "What 'Daemons-Tier' Means, Specifically" sections above, rather than held to the mainstream-franchise standard, which would filter every niche candidate back out even after finding them. If romance specifically is missing, check that the romance floor (SKILL.md Step 3 — at least 1 every run, non-negotiable) was actually applied, and that the YouTube-specific romance query was run, not just the general press query.

### A currently-airing romance title with real YouTube traction never showed up

This happened on the run that missed Seihantai na Kimi to Boku Season 2 and Smoking Behind the Supermarket With You — both mid-run Summer 2026 titles with genuine, active, episode-by-episode reaction coverage across multiple channels. Two compounding causes:

1. The "new release" romance query is framed around what's newly announced/premiering — it structurally excludes a title that premiered a cour or two ago and is still airing. Fix: always run the separate "currently airing" check (pull the season's full romance lineup, not just new-release search results) — see Search Strategy above.
2. A generic "romance anime trending YouTube" query returns ranked listicle videos, not the actual per-show reaction landscape — it will miss a title with real, consistent episode reactions that simply hasn't been packaged into a "Top 10" video yet. Fix: search by candidate title specifically (`"[title]" reaction YouTube episode`) once the currently-airing lineup is in hand, and look for the real signal (multiple channels, recent dated uploads, episode numbers tracking the air schedule) rather than the word "trending" in a result.

Remember WebSearch has no direct visibility into YouTube's own trending/algorithm data — every "YouTube-side" signal in this skill is a proxy built from searching for pages that reference YouTube content, not live platform analytics. Don't present a proxy-based finding with more confidence than that limitation supports.

### Output still reads like a pitch, not research

This was the core problem with the skill's prior version. If a write-up includes a "why you should make this" line, an invented catchy title, or language matched to some assumed channel voice, that's the old behavior leaking back in — strip it back to plain facts, context, volume, and saturation only.
