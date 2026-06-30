# Day Plan Reference

Complete reference for the day-plan skill. Covers all modes, argument usage, priority tiers, task format, meetings, deadlines, goals, file storage, time block scheduling, output templates, customization, and troubleshooting.

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
| `goals` | Goals Setup | Any time — set up or update monthly and quarterly goals |

**Examples:**
```
/day-plan              → Today's day plan
/day-plan week         → Start this week's planning session
/day-plan recap        → End-of-day wrap-up
/day-plan review       → End-of-week review
/day-plan update       → Edit the week plan
/day-plan add [work] Fix onboarding bug PRIORITIZE [2h]
/day-plan goals        → Set up or update monthly/quarterly goals
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

Tasks can be written in natural language. The skill recognises optional tags, estimates, and deadlines inline.

**Full format:**
```
[category] Task description TIER [Xh or Xm] [due: YYYY-MM-DD]
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

**Deadline tag** (optional):
- `[due: YYYY-MM-DD]` — the date this task must be completed
- Tasks due today or within 2 days are flagged as ⚠️ warnings at the top of the daily plan
- Overdue tasks (past their due date) are also flagged

**Examples:**
```
[work] Finish Q2 report PRIORITIZE [3h] [due: 2026-07-04]
[personal] Gym session HIGH [1h]
Reply to emails STANDARD [30m]
[work] Review PR QUICK WIN [15m]
Weekly standup RECURRING [30m]
Submit tax filing PRIORITIZE [due: 2026-07-31]
```

---

## Meeting Format

Meetings are handled separately from tasks because they block fixed time slots rather than being scheduled into them.

**Recurring meetings** (stored in the week plan — entered during `/day-plan week`):
```
[Meeting name] — [Day] [HH:MM AM – HH:MM AM]
```
Example: `Team standup — Monday 9:00 AM – 9:30 AM`

**One-off meetings** (entered during daily plan generation, not saved to the week file):
- Entered as: name, start time, duration
- Example: `1:1 with manager — 2:00 PM, 1 hour`

**How meetings affect scheduling:**
- All meeting slots are blocked out of available working hours before tasks are assigned to blocks
- If a meeting falls in the middle of a time block, the block is split around it
- Meeting durations are excluded from the "estimated total" overload check

---

## File Storage Reference

All plan files are saved to `z:\KARL\plans\` and goals to `z:\KARL\goals\`.

| File | What it stores |
|------|---------------|
| `plans/week-YYYY-WW.md` | The full week plan — all tasks, tiers, completion status, recurring meetings |
| `plans/YYYY-MM-DD.md` | A single day's plan — time-blocked schedule for that day |
| `goals/quarterly.md` | Quarterly goals (3–5 goals per quarter, updated in place each quarter) |
| `goals/monthly.md` | Monthly goals tied to quarterly goals (2–4 goals, updated in place each month) |

**Naming examples:**
```
plans/week-2026-21.md       → Week 21 of 2026 (May 18–24)
plans/2026-05-20.md         → Wednesday, May 20 2026
goals/quarterly.md          → Current quarter's goals
goals/monthly.md            → Current month's goals
```

**File lifecycle:**
- Week files are never deleted — completed tasks stay marked `[x]` for the weekly review.
- Day files are generated fresh each morning from the week file.
- Goals files are updated in place — overwritten when you run `/day-plan goals`.
- If `plans/` or `goals/` doesn't exist, the skill creates the directory on first run.

---

## Time Block Scheduling

When generating the daily plan, meeting times are blocked out first, then tasks fill the remaining slots.

**Default working hours:** 9:00 AM – 6:00 PM (9 hours, with 1 hour for breaks = 8 productive hours)

**Block structure:**

| Block | Time | Tasks assigned |
|-------|------|---------------|
| Morning | 9:00 AM – 12:00 PM | PRIORITIZE tasks |
| Midday | 12:00 PM – 2:00 PM | HIGH tasks (after ~30m lunch) |
| Afternoon | 2:00 PM – 5:30 PM | STANDARD tasks |
| Wrap-up | 5:30 PM – 6:00 PM | Admin, emails, quick review |

**Scheduling logic:**
1. Meeting times are blocked out of the schedule first.
2. Tasks with time estimates are placed into blocks sequentially — first task starts at block open time (or after the last meeting in that block), next starts after the previous ends.
3. Tasks without estimates are placed in the block but not given a specific time slot (listed as "flexible").
4. QUICK WIN tasks are never time-blocked — they appear as a standalone list.
5. If PRIORITIZE tasks overflow the morning block, they spill into midday before HIGH tasks.
6. If total estimated time (excluding meetings) exceeds 7 hours, the skill flags the overload and suggests deferring the lowest-priority items.

**Overload warning format:**
```
⚠️ Today's tasks total ~Xh (excluding meetings) — that's over the recommended 7h.
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

## 📅 Recurring Meetings
- [Meeting name] — [Day] [HH:MM AM – HH:MM AM]

## 🔴 PRIORITIZE
- [ ] [category] [task] [Xh] [due: YYYY-MM-DD]

## 🟠 HIGH
- [ ] [category] [task] [Xh]

## 🟡 STANDARD
- [ ] [category] [task]

## 🟢 QUICK WINS
- [ ] [category] [task] (~Xm)

## 🔁 RECURRING
- [ ] [task] [Xm]

---
_Created: YYYY-MM-DD | Last updated: YYYY-MM-DD_
```

---

### Daily Plan (`plans/YYYY-MM-DD.md`)

```markdown
# 📅 Day Plan — [Weekday, Month Day, Year]

_Carried over from yesterday: [task, task] — or "none"_

⚠️ Deadlines approaching:
- [work] Finish Q2 report — due today
- Submit filing — due tomorrow
(omit this block entirely if no deadlines within 2 days)

---

## 📅 Meetings
- Team standup [9:00 AM – 9:30 AM]
- 1:1 with manager [2:00 PM – 3:00 PM]
(omit this section if no meetings today)

## 🔁 Recurring
- [ ] Weekly standup [9:00 AM – 9:30 AM]

## 🔴 Morning Block (9:30 AM – 12:00 PM)
- [ ] [work] Finish Q2 report [9:30 AM – 12:00 PM] [2.5h]

## 🟠 Midday Block (12:30 PM – 2:00 PM)
- [ ] [personal] Gym session [12:30 PM – 1:30 PM] [1h]

## 🟡 Afternoon Block (3:00 PM – 5:30 PM)
- [ ] Reply to emails [3:00 PM – 3:30 PM] [30m]
- [ ] [task with no estimate — flexible]

## 🟢 Quick Wins (grab when you have a gap)
- [ ] [work] Review PR (~15m)

---
**Estimated total:** Xh Ym (excluding meetings)
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

## Goals Format Reference

### Quarterly Goals (`goals/quarterly.md`)

```markdown
# 🎯 Quarterly Goals — [Q1/Q2/Q3/Q4 YYYY]

- **Q1:** [goal]
- **Q2:** [goal]
- **Q3:** [goal]

_Updated: YYYY-MM-DD_
```

### Monthly Goals (`goals/monthly.md`)

```markdown
# 📅 Monthly Goals — [Month YYYY]

- **M1:** [goal] → Q[N]
- **M2:** [goal] → Q[N]
- **M3:** [goal] → Q[N]

_Updated: YYYY-MM-DD_
```

Goals files are updated in place each quarter/month. The skill reads both files at the start of weekly planning to surface the current context.

---

## Weekly Planning Session Flow

When the user runs `/day-plan week`, the skill runs a conversational Q&A. This is the exact flow:

**Step 1 — Surface current goals**
Read `goals/quarterly.md` and `goals/monthly.md`. Display a brief summary:
> "Your current quarterly goals are: [Q1, Q2, Q3]. This month's focus is: [M1, M2]. Keep these in mind as you build this week's task list."

If no goals files exist:
> "No goals are set yet. You can run `/day-plan goals` to set them up, or continue building this week's plan without them."

**Step 2 — Check for existing week plan**
If a file for the current week already exists, ask:
> "You have an existing week plan. Do you want to start fresh or carry forward incomplete tasks?"

If carrying forward: pre-populate the new week file with all `[ ]` tasks from last week, keeping their tiers.

**Step 3 — Week focus**
> "What's the focus or theme for this week? (One sentence — e.g. 'Ship the v2 feature' or 'Catch up on admin')"

**Step 4 — PRIORITIZE tasks**
> "What are your PRIORITIZE tasks this week? These are must-complete items. List them one per line, with an optional time estimate like [2h], category tag like [work] or [personal], and deadline like [due: 2026-07-04]."

**Step 5 — HIGH tasks**
> "What are your HIGH priority tasks? You strongly intend to finish these this week."

**Step 6 — STANDARD tasks**
> "Any STANDARD tasks? These are nice-to-dos if time allows."

**Step 7 — QUICK WINS**
> "Any quick tasks under 15 minutes? These are great for filling gaps between bigger items."

**Step 8 — RECURRING tasks**
> "Any recurring tasks that happen every week? (e.g. team standup, weekly report)"

**Step 9 — Recurring meetings**
> "Any recurring meetings this week that should block time on specific days? (e.g. Monday standup 9:00–9:30 AM, Wednesday team sync 2:00–3:00 PM)"

**Step 10 — Confirm and save**
Display the full week plan for review, then save to `plans/week-YYYY-WW.md`. Offer to generate today's day plan immediately.

**Skipping steps:** If the user says "none" or leaves a tier blank, skip to the next one without re-asking.

---

## Customization

### Changing Working Hours

To change the default 9 AM – 6 PM schedule, edit SKILL.md and update the block times in Mode 2 (Daily Plan). The block structure is:
- Morning: start of day → lunch
- Midday: after lunch → mid-afternoon
- Afternoon: mid-afternoon → end of day

### Changing the Overload Threshold

The default overload warning fires at 7 hours of estimated tasks (excluding meetings). To change this, edit the threshold in SKILL.md Mode 2, Step 6.

### Changing the Deadline Warning Window

By default, tasks with deadlines within 2 days are flagged. To change this window, edit Step 4 in Mode 2 (Daily Plan) in SKILL.md to a different number of days.

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

### Goals not showing during weekly planning

The skill reads `goals/quarterly.md` and `goals/monthly.md` at the start of Mode 1. If these files don't exist, the skill notes this and suggests running `/day-plan goals`. Run that mode first to create the files, then return to weekly planning.

### Deadlines not being flagged

Check that the deadline is formatted exactly as `[due: YYYY-MM-DD]` in the task line in the week plan file. Any deviation (e.g. `due: Jul 4` or `[deadline: 2026-07-04]`) will not be recognised. Use `/day-plan update` to correct the format.

### Meetings overlapping with tasks in the daily plan

If a meeting wasn't in the Recurring Meetings section of the week plan and wasn't entered during daily plan generation, it won't be blocked out. Add it using `/day-plan update` (to add a recurring meeting to the week plan) or re-run `/day-plan` and enter it as a one-off meeting when prompted.
