---
name: day-plan
description: Use when someone asks to plan their day, plan their week, organize tasks, set priorities, do an end-of-day recap, or review their week.
argument-hint: [week | recap | review | update | add <task>]
allowed-tools: Read, Write, Glob
---

For the complete reference on modes, file storage, output format, and customization, see [reference.md](reference.md).

## What This Skill Does

Helps plan and manage tasks across the week and day. Maintains a persistent weekly plan file and generates focused daily plans from it. Supports priority tiers, time estimates, quick wins, task carryover, and end-of-day/week reviews.

Plans are saved to the `plans/` folder so they persist across sessions.

## Modes

Behavior is driven by `$ARGUMENTS`:

| Argument | Mode |
|----------|------|
| _(empty)_ | Generate today's day plan from the current week plan |
| `week` | Start a new weekly planning session |
| `recap` | End-of-day recap — what's done, what carries over |
| `review` | End-of-week review — what shipped, what didn't, what moves on |
| `update` | Modify the current week plan (add, complete, or remove tasks) |
| `add <task>` | Quickly add a single task to the week plan |

---

## Mode 1: Weekly Planning (`week`)

Run at the start of each week. Use this to set the week's task list and focus.

**Steps:**

1. Check if a current week file exists at `plans/week-YYYY-WW.md`. If so, ask whether to start fresh or carry forward incomplete tasks from last week.

2. Ask the user for this week's tasks using AskUserQuestion or conversational prompts. Collect:
   - Tasks labeled **PRIORITIZE** — must be completed this week, scheduled first each day
   - Tasks labeled **HIGH** — strong intent to complete this week
   - Tasks labeled **STANDARD** — complete if time allows
   - Tasks labeled **QUICK WIN** — under 15 minutes, used to fill gaps
   - Tasks labeled **RECURRING** — happen every week (e.g., team standup, weekly report)
   - Optional: time estimates per task using `[Xh]` or `[Xm]` notation

3. Ask for one sentence describing this week's overall focus or theme.

4. Save the week plan to `plans/week-YYYY-WW.md` using the Weekly Plan template below.

5. Confirm the plan is saved and offer to generate today's day plan immediately.

**Weekly Plan template (`plans/week-YYYY-WW.md`):**

```markdown
# 📆 Week Plan — Week of [Month Day, Year]

**Focus:** [One sentence — what is this week about?]

---

## 🔴 PRIORITIZE
- [ ] [task] [time estimate if provided]

## 🟠 HIGH
- [ ] [task]

## 🟡 STANDARD
- [ ] [task]

## 🟢 QUICK WINS
- [ ] [task] (~Xm)

## 🔁 RECURRING
- [ ] [task]

---
_Created: [date] | Last updated: [date]_
```

---

## Mode 2: Daily Plan _(default, no argument)_

Generates a focused plan for today pulled from the current week plan.

**Steps:**

1. Read the current week plan from `plans/week-YYYY-WW.md`. If none exists, prompt the user to run `/day-plan week` first.

2. Check for yesterday's day plan at `plans/YYYY-MM-DD.md`. Extract any tasks marked `[ ]` (incomplete) — these are carryovers.

3. Build today's plan:
   - **Morning block:** All PRIORITIZE tasks (incomplete from week plan + carryovers)
   - **Midday block:** HIGH tasks
   - **Afternoon block:** STANDARD tasks
   - **Fill gaps:** QUICK WIN tasks surfaced separately for in-between moments
   - **Recurring:** Always appear at the top of their relevant time

4. Calculate estimated total time from task estimates. If the total exceeds ~7 hours, flag it and suggest which tasks to defer to tomorrow.

5. Save the day plan to `plans/YYYY-MM-DD.md` and display it.

**Daily Plan template (`plans/YYYY-MM-DD.md`):**

```markdown
# 📅 Day Plan — [Weekday, Month Day]

## 🔁 Recurring
- [ ] [task]

## 🔴 Priority (Morning)
- [ ] [task] [Xh]

## 🟠 High Priority (Midday)
- [ ] [task] [Xh]

## 🟡 Standard (Afternoon)
- [ ] [task]

## 🟢 Quick Wins
- [ ] [task] (~Xm)

---
**Estimated total:** Xh Ym
_Carried over from yesterday: [task list or "none"]_
```

---

## Mode 3: End-of-Day Recap (`recap`)

Run at the end of the day to close out and set up tomorrow.

**Steps:**

1. Read today's day plan from `plans/YYYY-MM-DD.md`.

2. Ask the user which tasks were completed. Update the file, marking completed tasks with `[x]`.

3. List all remaining `[ ]` tasks — these will carry forward to tomorrow's plan.

4. Ask if anything new came up today that should be added to the week plan.

5. Update `plans/week-YYYY-WW.md` with any changes — newly completed tasks marked `[x]`, any additions appended.

6. Display a short recap: tasks done, tasks carrying over, and a one-line note on how the day went (optional, user-supplied).

---

## Mode 4: Weekly Review (`review`)

Run at the end of the week (Friday or whenever the week closes).

**Steps:**

1. Read the week plan from `plans/week-YYYY-WW.md` and all daily plan files for the week (`plans/YYYY-MM-DD.md` × 5).

2. Tally: how many PRIORITIZE tasks were completed vs missed, and same for HIGH and STANDARD.

3. List all incomplete tasks and ask the user: carry forward to next week, drop, or reschedule?

4. Ask for a one-sentence reflection on the week.

5. Display the weekly review summary using the template below.

6. Offer to immediately start next week's planning session (`/day-plan week`).

**Weekly Review template:**

```markdown
# 📊 Week Review — Week of [Month Day, Year]

**Focus:** [original focus]
**Reflection:** [user's one-sentence note]

## Results
| Tier | Completed | Total |
|------|-----------|-------|
| 🔴 PRIORITIZE | X | X |
| 🟠 HIGH | X | X |
| 🟡 STANDARD | X | X |

## Carrying Forward
- [ ] [task] → next week

## Dropped
- [task] — [reason if given]
```

---

## Mode 5: Update (`update`)

Modify the current week plan mid-week.

**Steps:**

1. Read `plans/week-YYYY-WW.md` and display the current task list.

2. Ask what the user wants to change: add a task, complete a task, remove a task, change a priority, or add a time estimate.

3. Apply the changes and save the updated file with a new `Last updated` timestamp.

4. Confirm the update and ask if they'd like to regenerate today's day plan with the changes applied.

---

## Mode 6: Quick Add (`add <task>`)

Add a single task to the week plan without going through the full update flow.

**Steps:**

1. Parse `$ARGUMENTS` — everything after `add` is the task description. Look for a priority keyword (PRIORITIZE, HIGH, STANDARD, QUICK WIN) and time estimate (`[Xh]`/`[Xm]`) in the text.

2. If no priority keyword is found, default to STANDARD.

3. Append the task to the correct section in `plans/week-YYYY-WW.md` and update the `Last updated` timestamp.

4. Confirm: "Added '[task]' to [tier] for this week."

---

## Notes

- Always read the existing week plan file before generating a day plan — never make up tasks.
- If `plans/` does not exist, create it before writing the first file.
- Never delete a week plan file — completed tasks stay in the file marked `[x]` for the weekly review.
- If the user's daily total exceeds ~7 hours of estimated tasks, flag the overload and suggest deferring lowest-priority items rather than silently cutting them.
- QUICK WIN tasks should not anchor a time block — surface them as a separate list for opportunistic moments between tasks.
- Recurring tasks always appear regardless of the day, unless the user says a specific one doesn't apply today.
