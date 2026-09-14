# YT Ideas Reference

Complete reference for the yt-ideas skill. Covers search strategy, the volume-of-interest bar, source priority, output format details, customization, and troubleshooting.

---

## Argument Reference

`$ARGUMENTS` is optional. The first token decides the mode.

| Input | Behavior |
|-------|----------|
| _(empty)_ | Surface 3–5 new ranked video ideas |
| `develop <idea or topic>` | Draft a hook + outline for that idea |

**Examples:**
```
/yt-ideas                              → New ranked idea suggestions
/yt-ideas develop the Season 3 finale  → Hook + outline for that idea
```

---

## Search Strategy

### Date Range Logic

1. **Past 24–48 hours** for viral clips/moments and discourse — these move fast and go stale within days.
2. **Past week** for episode/season releases and announcements — acceptable to look slightly further back since a release's discussion tail lasts longer than a single clip's.
3. **Always disclose** if you had to widen beyond 48 hours to find enough material.

### Constructing Search Queries

Run separate searches per angle — a single broad query lets one loud story crowd out everything else.

| Angle | Example query |
|-------|---------------|
| New releases | `anime new episode OR season premiere this week 2026` |
| Controversy/discourse | `anime controversy OR debate OR discourse trending 2026` |
| Announcements | `anime announcement OR adaptation OR casting news 2026` |
| Viral moments | `anime viral clip OR scene OR reaction trending 2026` |
| Your channel | `"GunnnarZ" YouTube anime` |
| Competitor discovery | `anime YouTube commentary channel [subscriber range or niche descriptor]` |

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

## The Volume-of-Interest Bar

This is the core judgment call in Mode 1 Step 4 — what actually counts as "has real buzz" versus "one person's take."

**Clears the bar:**
- Multiple independent sources (2+ outlets, or an outlet plus visible community discussion) covering the same topic within the same window
- A source that cites a concrete signal: a trending chart position, a specific view/engagement count, a notably large or fast-growing discussion thread
- A topic several unrelated commentary channels are covering around the same time (a genuine sign it's "in the air," not just algorithmically pushed to one search)

**Doesn't clear the bar on its own:**
- A single blog post or forum thread with no corroboration
- A topic that's evergreen/always-discussed rather than freshly spiking (e.g., a long-running "best anime of all time" debate has no real "why now")
- Something that only your own search phrasing surfaced, with no independent trace of it elsewhere

**When in doubt:** include it but say so plainly in the "Buzz" line — e.g., "Buzz: moderate — one detailed Reddit thread (400+ comments) but no press coverage yet." Never round a weak signal up to sound more confident than it is.

---

## Output Format Reference

See SKILL.md for the full templates. Key rules:

**Idea titles:** Punchy and specific — name the actual show/character/event, not a vague category. "Why [Character]'s Season 3 Turn Is Splitting the Fandom" beats "Thoughts on Season 3."

**Trend line:** State the actual fact with specifics (what happened, when, who's involved) — not just "this is popular right now."

**Why it fits line:** Must tie to something concrete about the channel — a past video, an established series/format, a recurring angle — not a generic "this seems like your style."

**Buzz line:** Must name the actual evidence (source count, an engagement number, cross-channel coverage) — never just assert "high interest" with nothing behind it.

**Competitor angle line:** Optional — omit entirely rather than writing a filler sentence when nothing notable turned up from competitor research.

---

## Customization

### Adjusting Idea Count

Change "3–5" in SKILL.md Mode 1 Step 7 if 5 feels like too many to evaluate at once, or too few on a busy week.

### Widening Beyond Anime

The channel focus is currently hardcoded to anime per the current channel identity. If the channel's focus broadens later, update SKILL.md's "What This Skill Does" section and the search queries in this file's Search Strategy table accordingly — this was deliberately kept anime-only rather than built with a topic argument, so revisit that decision if it stops fitting.

### Scheduling as a Recurring Check

`/schedule` can run `/yt-ideas` on a recurring basis (e.g., weekly) the same way tech-update and morning-coffee can. `develop` is an interactive follow-up and isn't a good candidate for unattended scheduling — only schedule the default idea-surfacing mode.

---

## Troubleshooting

### WebFetch on a YouTube URL returns nothing useful

Expected — YouTube channel and video pages are JS-rendered and WebFetch only sees the static footer/nav shell. Never rely on WebFetch for YouTube itself; use WebSearch for channel research, competitor discovery, and buzz signals throughout.

### Ideas feel repetitive across runs

Check that `content/yt-ideas.md` is actually being read in Mode 1 Step 1 before generating new suggestions — if the file is being skipped, past ideas won't get cross-checked. Also confirm the channel's own video history (Step 2) is being searched fresh each run, not cached from memory.

### An idea's "Buzz" claim doesn't hold up on a second look

This is the same failure mode documented in morning-coffee and tech-update's reference docs: a claim not traced to a real, checkable source. Every "Buzz" line must name real evidence — if you can't point to the specific source(s) behind a volume claim, the honest move is to downgrade the confidence language or drop the idea, not present it with false certainty.

### Too many/too few ideas clearing the bar

If most weeks produce fewer than 3 solid ideas, the volume-of-interest bar in this file may be too strict for how niche your corner of anime commentary is — consider loosening the "2+ independent sources" requirement to allow one strong Tier 2 source alone. If every run maxes out at 5 with room to spare, consider raising the count in SKILL.md Mode 1 Step 7.
