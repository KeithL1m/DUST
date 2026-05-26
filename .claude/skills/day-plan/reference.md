# Day Plan Reference

Complete reference for the day-plan skill. Covers all modes, argument usage, priority tiers, file storage, task format, time block scheduling, output templates, customization, and troubleshooting.

---

## Argument Reference

`$ARGUMENTS` controls which mode runs. All arguments are case-insensitive.

| Argument | Mode | When to use |
|----------|------|-------------|
| _(empty)_ | Daily Plan | Start of day — generate today's plan from the week file |
| `week` | Weekly Planning | Start of week — input tasks, set focus, build the week plan |
| `recap` | End-of-Day Recap | End of day — mark done tasks, surface carryovers |
| `review` | Weekly Review | End of week — tally results, handle incomplete tasks |
| `update` | Update Plan | Mid-week — add, remove, reprioritize, or complete tasks |
| `add <task>` | Quick Add | Any time — append a single task without the full update flow |

**Examples:**
```
/day-plan              → Today's day plan
/day-plan week         → Start this week's planning session
/day-plan recap        → End-of-day wrap-up
/day-plan review       → End-of-week review
/day-plan update       → Edit the week plan
/day-plan add [work] Fix onboarding bug PRIORITIZE [2h]
```

---

## Priority Tier System

Five tiers control how tasks are ordered and scheduled.

| Tier | Label | Meaning | Daily placement |
|------|-------|---------|-----------------|
| 🔴 | `PRIORITIZE` | Must complete — highest urgency or importance | Morning block, first |
| 🟠 | `HIGH` | Strong intent to finish today | Midday block |
| 🟡 | `STANDARD` | Complete if time allows | Afternoon block |
| 🟢 | `QUICK WIN` | Under 15 minutes — fill gaps between tasks | Surfaced separately |
| 🔁 | `RECURRING` | Happens every week regardless | Top of day, always shown |

**Rules:**
- A task with no tier keyword defaults to STANDARD.
- QUICK WIN tasks are never anchored to a time block — they appear as a separate list for opportunistic moments.
- RECURRING tasks always appear on the daily plan unless the user says a specific one doesn't apply today.
- Only PRIORITIZE tasks trigger the overload warning when the day is over-scheduled.

---

## Task Format

Tasks can be written in natural language. The skill recognises optional tags and estimates inline.

**Full format:**
```
[category] Task description TIER [Xh or Xm]
```

**Category tags** (optional):
- `[work]` — professional task
- `[personal]` — personal task
- Omit the tag if you don't want to categorize it

**Time estimates** (optional):
- `[2h]` — 2 hours
- `[30m]` — 30 minutes
- `[1h30m]` — 1 hour 30 minutes
- Omit if unknown — the skill will still schedule the task but won't include it in the total

**Examples:**
```
[work] Finish Q2 report PRIORITIZE [3h]
[personal] Gym session HIGH [1h]
Reply to emails STANDARD [30m]
[work] Review PR QUICK WIN [15m]
Weekly standup RECURRING [30m]
```

---

## File Storage Reference

All plan files are saved to `d:\KARL\plans\`.

| File | What it stores |
|------|---------------|
| `plans/week-YYYY-WW.md` | The full week plan — all tasks, tiers, completion status |
| `plans/YYYY-MM-DD.md` | A single day's plan — time-blocked schedule for that day |

**Naming examples:**
```
plans/week-2026-21.md       → Week 21 of 2026 (May 18–24)
plans/2026-05-20.md         → Wednesday, May 20 2026
```

**File lifecycle:**
- Week files are never deleted — completed tasks stay marked `[x]` for the weekly review.
- Day files are generated fresh each morning from the week file.
- If the `plans/` directory doesn't exist, the skill creates it on first run.

---

## Time Block Scheduling

When generating the daily plan, tasks are mapped to approximate time slots.

**Default working hours:** 9:00 AM – 6:00 PM (9 hours, with 1 hour for breaks = 8 productive hours)

**Block structure:**

| Block | Time | Tasks assigned |
|-------|------|---------------|
| Morning | 9:00 AM – 12:00 PM | PRIORITIZE tasks |
| Midday | 12:00 PM – 2:00 PM | HIGH tasks (after ~30m lunch) |
| Afternoon | 2:00 PM – 5:30 PM | STANDARD tasks |
| Wrap-up | 5:30 PM – 6:00 PM | Admin, emails, quick review |

**Scheduling logic:**
1. Tasks with time estimates are placed into blocks sequentially — first task in a block starts at block open time, next starts after the previous ends.
2. Tasks without estimates are placed in the block but not given a specific time slot (listed as "flexible").
3. QUICK WIN tasks are never time-blocked — they appear as a standalone list.
4. If PRIORITIZE tasks overflow the morning block, they spill into midday before HIGH tasks.
5. If total estimated time exceeds 7 hours, the skill flags the overload and suggests deferring the lowest-priority items.

**Overload warning format:**
```
⚠️ Today's tasks total ~Xh — that's over the recommended 7h.
Consider deferring:
- [task] [Xh] → tomorrow or later this week
```

**Customizing working hours:** See the Customization section below.

---

## Output Format Reference

### Weekly Plan (`plans/week-YYYY-WW.md`)

```markdown
# 📆 Week Plan — Week of [Month Day, Year]

**Focus:** [One sentence — what is this week about?]

---

## 🔁 RECURRING
- [ ] [task] [Xm]

## 🔴 PRIORITIZE
- [ ] [category] [task] [Xh]

## 🟠 HIGH
- [ ] [category] [task] [Xh]

## 🟡 STANDARD
- [ ] [category] [task]

## 🟢 QUICK WINS
- [ ] [category] [task] (~Xm)

---
_Created: YYYY-MM-DD | Last updated: YYYY-MM-DD_
```

---

### Daily Plan (`plans/YYYY-MM-DD.md`)

```markdown
# 📅 Day Plan — [Weekday, Month Day, Year]

_Carried over from yesterday: [task, task] — or "none"_

---

## 🔁 Recurring
- [ ] Weekly standup [9:00 AM – 9:30 AM]

## 🔴 Morning Block (9:00 AM – 12:00 PM)
- [ ] [work] Finish Q2 report [9:00 AM – 12:00 PM] [3h]

## 🟠 Midday Block (12:30 PM – 2:00 PM)
- [ ] [personal] Gym session [12:30 PM – 1:30 PM] [1h]

## 🟡 Afternoon Block (2:00 PM – 5:30 PM)
- [ ] Reply to emails [2:00 PM – 2:30 PM] [30m]
- [ ] [task with no estimate — flexible]

## 🟢 Quick Wins (grab when you have a gap)
- [ ] [work] Review PR (~15m)

---
**Estimated total:** Xh Ym
```

---

### End-of-Day Recap

Displayed in conversation — not saved as a separate file. Changes are applied back to the week plan file.

```
## 📋 Day Recap — [Weekday, Month Day]

✅ Completed (X tasks)
- [task]
- [task]

⏭ Carrying over to tomorrow
- [task]
- [task]

📝 Added today
- [task] → added to [tier] in week plan

---
Nice work today. X of Y tasks done.
```

---

### Weekly Review

Displayed in conversation — not saved as a separate file.

```
## 📊 Week Review — Week of [Month Day, Year]

**Focus:** [original focus]
**Reflection:** [user's one-sentence note]

### Results
| Tier        | Completed | Total | % |
|-------------|-----------|-------|---|
| 🔴 PRIORITIZE | X       | X     | % |
| 🟠 HIGH       | X       | X     | % |
| 🟡 STANDARD   | X       | X     | % |

### Carrying Forward to Next Week
- [ ] [task] — [original tier]

### Dropped This Week
- [task] — [reason if given, or "no longer needed"]

---
X of Y tasks completed this week.
```

---

## Weekly Planning Session Flow

When the user runs `/day-plan week`, the skill runs a conversational Q&A — one tier at a time. This is the exact flow:

**Step 1 — Check for existing week plan**
If a file for the current week already exists, ask:
> "You have an existing week plan. Do you want to start fresh or carry forward incomplete tasks?"

If carrying forward: pre-populate the new week file with all `[ ]` tasks from last week, keeping their tiers.

**Step 2 — Week focus**
> "What's the focus or theme for this week? (One sentence — e.g. 'Ship the v2 feature' or 'Catch up on admin')"

**Step 3 — PRIORITIZE tasks**
> "What are your PRIORITIZE tasks this week? These are must-complete items. List them one per line, with an optional time estimate like [2h] and category tag like [work] or [personal]."

**Step 4 — HIGH tasks**
> "What are your HIGH priority tasks? You strongly intend to finish these this week."

**Step 5 — STANDARD tasks**
> "Any STANDARD tasks? These are nice-to-dos if time allows."

**Step 6 — QUICK WINS**
> "Any quick tasks under 15 minutes? These are great for filling gaps between bigger items."

**Step 7 — RECURRING tasks**
> "Any recurring tasks that happen every week? (e.g. team standup, weekly report)"

**Step 8 — Confirm and save**
Display the full week plan for review, then save to `plans/week-YYYY-WW.md`. Offer to generate today's day plan immediately.

**Skipping tiers:** If the user says "none" or leaves a tier blank, skip to the next one without re-asking.

---

## Customization

### Changing Working Hours

To change the default 9 AM – 6 PM schedule, edit SKILL.md and update the block times in Mode 2 (Daily Plan). The block structure is:
- Morning: start of day → lunch
- Midday: after lunch → mid-afternoon
- Afternoon: mid-afternoon → end of day

### Changing the Overload Threshold

The default overload warning fires at 7 hours of estimated tasks. To change this, edit the threshold in SKILL.md Mode 2, Step 4.

### Turning Off Category Tags

If you don't want `[work]` / `[personal]` tags, simply don't use them — the skill treats untagged tasks normally. No config change needed.

### Adjusting Quick Win Time Threshold

The default Quick Win threshold is 15 minutes. If a task has an estimate of 15m or less, it's treated as a Quick Win regardless of its tier label. To change this threshold, update the note in SKILL.md.

### Adding Custom Tiers

To add a tier (e.g. `SOMEDAY` for a backlog), add a new section to the weekly plan template and a corresponding block in the daily plan logic in SKILL.md. Reference the tier keyword in the task format section above so the skill recognises it.

---

## Troubleshooting

### No week plan found when running `/day-plan`

The skill looks for `plans/week-YYYY-WW.md` matching the current week number. If the file doesn't exist, it prompts you to run `/day-plan week` first. This also happens if the file was saved with an incorrect week number — check the filename matches the current ISO week.

### Tasks not carrying over from yesterday

The skill reads yesterday's file at `plans/YYYY-MM-DD.md` and looks for unchecked `[ ]` items. If the file doesn't exist (e.g. you didn't run `/day-plan` yesterday), there will be nothing to carry over — this is expected.

### Daily plan feels overloaded every day

Either the weekly task list has too many PRIORITIZE/HIGH items, or time estimates are optimistic. Use `/day-plan update` to drop tasks to STANDARD or QUICK WIN, or reduce time estimates. The 7-hour overload warning is a signal to do this proactively.

### `/day-plan recap` isn't updating the week file

The recap flow reads today's day plan, then writes back to the week file. If the week file path doesn't match (e.g. week number rolled over mid-day), the skill will say so. Confirm both files are in `plans/` with correct names.

### Week numbering confusion

ISO week numbers are used (`YYYY-WW`). Week 1 is the week containing the first Thursday of the year. You can check the current week number by asking "what ISO week is today?" — the skill will calculate it from the current date.
