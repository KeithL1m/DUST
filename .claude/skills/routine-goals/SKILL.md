---
name: routine-goals
description: Use when someone asks to plan their day, plan their week, organize tasks, set priorities, do an end-of-day recap, review their week, track a habit streak, or journal a daily reflection.
argument-hint: [week | recap | review | update | add <task> | goals | habits]
allowed-tools: Read, Write, Glob
---

For the complete reference on modes, file storage, output format, and customization, see [reference.md](reference.md).

## What This Skill Does

Helps plan and manage tasks across the week and day, and connects that daily action back to your goals and motivation. Maintains a persistent weekly plan file and generates focused daily plans from it. Supports priority tiers, time estimates, deadlines, meetings, goal alignment, quick wins, task carryover, habit streaks, end-of-day reflection journaling, and end-of-day/week reviews.

Plans, goals, habits, and journal entries are saved to `plans/`, `goals/`, `habits/`, and `journal/` — all four are gitignored (private, local-only).

## Modes

Behavior is driven by `$ARGUMENTS`:

| Argument | Mode |
|----------|------|
| _(empty)_ | Generate today's day plan from the current week plan |
| `week` | Start a new weekly planning session |
| `recap` | End-of-day recap — tasks done, habit check-in, reflection journal entry |
| `review` | End-of-week review — what shipped, habit streaks, reflection highlights |
| `update` | Modify the current week plan (add, complete, or remove tasks) |
| `add <task>` | Quickly add a single task to the week plan |
| `goals` | Set up or update monthly and quarterly goals |
| `habits` | Set up or update recurring habits tied to a goal |

---

## Mode 1: Weekly Planning (`week`)

Run at the start of each week. Use this to set the week's task list and focus.

**Steps:**

1. Read `goals/quarterly.md` and `goals/monthly.md` if they exist. Display the current goals briefly so the user can align this week's tasks with them. If no goals files exist, suggest running `/routine-goals goals` to set them up first.

2. Check if a current week file exists at `plans/week-YYYY-WW.md`. If so, ask whether to start fresh or carry forward incomplete tasks from last week.

3. Ask for one sentence describing this week's overall focus or theme.

4. Ask the user for this week's tasks using AskUserQuestion or conversational prompts. Collect:
   - Tasks labeled **PRIORITIZE** — must be completed this week, scheduled first each day
   - Tasks labeled **HIGH** — strong intent to complete this week
   - Tasks labeled **STANDARD** — complete if time allows
   - Tasks labeled **QUICK WIN** — under 15 minutes, used to fill gaps
   - Tasks labeled **RECURRING** — happen every week (e.g., team standup, weekly report)
   - Optional: time estimates per task using `[Xh]` or `[Xm]` notation
   - Optional: deadlines per task using `[due: YYYY-MM-DD]` notation

5. Ask for recurring meetings this week — meetings that happen on the same day and time every week (e.g., Monday standup 9:00–9:30 AM). Store these in the Recurring Meetings section of the week plan. One-off meetings are entered during each daily plan.

6. Save the week plan to `plans/week-YYYY-WW.md` using the Weekly Plan template below.

7. Confirm the plan is saved and offer to generate today's day plan immediately.

**Weekly Plan template (`plans/week-YYYY-WW.md`):**

```markdown
# 📆 Week Plan — Week of [Month Day, Year]

**Focus:** [One sentence — what is this week about?]

---

## 📅 Recurring Meetings
- [Meeting name] — [Day] [HH:MM AM – HH:MM AM]

## 🔴 PRIORITIZE
- [ ] [task] [time estimate if provided] [due: YYYY-MM-DD if provided]

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

1. Determine the current ISO week's file path, `plans/week-YYYY-WW.md`. If it doesn't exist, auto-create it silently using the Weekly Plan template — focus line set to `_(no weekly session run this week)_`, all tier sections empty. No interview, no confirmation needed; this just gives the day-to-day flow below a place to accumulate tasks. (If the user wants the full interview instead, they can always run `/routine-goals week`.) Then read the week plan and goals files from `goals/`.

2. Check for yesterday's day plan at `plans/YYYY-MM-DD.md`. If there are incomplete `[ ]` tasks, show them all at once and ask the user to decide for each: **keep** (carry to today), **defer** (push to a specific later day this week — ask which day), or **drop**. Apply those decisions before building today's plan, and update the week plan to reflect any deferred or dropped tasks.

3. Ask if there's anything new to get done today (or later this week) beyond what's already carrying over — same format as Quick Add: task description, priority tier (PRIORITIZE/HIGH/STANDARD/QUICK WIN/RECURRING), and optional `[Xh]`/`[Xm]`, `[due: YYYY-MM-DD]`, `[goal: M#/Q#]` tags. Append anything given to the correct tier in the week plan file. If the user says nothing new, move on — don't force an answer.

4. Ask for today's one-off meetings — any meetings not already in the week plan's Recurring Meetings section (name, time, duration). These appear in today's plan only and are not saved to the week file.

5. Scan all tasks in the week plan for `[due:]` tags. Flag any tasks due today or within the next 2 days as a ⚠️ deadline warning block at the top of the day plan.

6. Read `habits/habits.md` if it exists. Determine which habits are due today based on their frequency (see Mode 8). List them in a 🔥 Habits block — this is a reminder only, habits are checked off during `recap`, not here.

7. Scan tasks for an optional `[goal: M#]` or `[goal: Q#]` tag (see Mode 7). If any of today's PRIORITIZE or HIGH tasks carry one, add a single 🎯 line naming which goal(s) today's work moves forward — pull the goal's text from `goals/monthly.md` or `goals/quarterly.md` so it reads as a real sentence, not just an ID.

8. Build today's plan, blocking out all meeting times (recurring + one-off) so tasks are not scheduled during meetings:
   - **Morning block:** All PRIORITIZE tasks (incomplete from week plan + kept carryovers)
   - **Midday block:** HIGH tasks
   - **Afternoon block:** STANDARD tasks
   - **Fill gaps:** QUICK WIN tasks surfaced separately for in-between moments
   - **Recurring tasks:** Always appear at the top of their relevant time

9. Calculate estimated total time excluding meeting durations. If the total exceeds ~7 hours, flag it and suggest which tasks to defer.

10. Save the day plan to `plans/YYYY-MM-DD.md` and display it.

**Daily Plan template (`plans/YYYY-MM-DD.md`):**

```markdown
# 📅 Day Plan — [Weekday, Month Day]

⚠️ Deadlines approaching:
- [task] — due [today / tomorrow / in 2 days]
(omit this block entirely if no deadlines within 2 days)

🎯 Today moves [goal name(s)] forward.
(omit this line if no task today carries a [goal:] tag)

## 📅 Meetings
- [Meeting name] [HH:MM AM – HH:MM AM]
(omit this section if no meetings today)

## 🔥 Habits
- [ ] [habit] — [N]-day streak
(omit this section if no habits are due today or habits/habits.md doesn't exist)

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
**Estimated total:** Xh Ym (excluding meetings)
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

6. If `habits/habits.md` exists, ask a single yes/no per habit that was due today (batch them in one question, not one at a time). For each: if yes, increment its streak by 1. If no: on a **weekday** (Mon–Fri), reset the streak to 0; on a **weekend day** (Sat/Sun), leave the streak unchanged — weekends are a no-penalty grace zone, done if motivated, no break if not. Update `Last Check-in` to today's date either way, and save `habits/habits.md`. Note any streak that just hit a milestone (7, 14, 30, 60, 100 days) — this gets called out in the recap display.

7. Ask two short reflection prompts: "What went well today?" and "What got in the way, if anything?" Both are optional — if the user skips either, write "—" rather than inventing an answer. Also ask for an optional freeform motivation note (a sentence on how they're feeling about their goals, or nothing at all).

8. Append the reflection to `journal/YYYY-MM.md` (create the file with a month header if it doesn't exist yet; create `journal/` if it doesn't exist). Never overwrite a previous day's entry — this file is append-only.

9. Display a recap combining: tasks done, tasks carrying over, habit streaks (with any milestone called out), and the reflection just recorded.

**Recap display template:**

```markdown
## 📋 Day Recap — [Weekday, Month Day]

✅ Completed (X tasks)
- [task]

⏭ Carrying over to tomorrow
- [task]

🔥 Habits
- [habit] — [N]-day streak [🎉 milestone! if applicable]
- [habit] — streak reset (missed today)
(omit this section if habits/habits.md doesn't exist)

📝 Reflection logged
**Went well:** [note]
**Got in the way:** [note]

---
Nice work today. X of Y tasks done.
```

---

## Mode 4: Weekly Review (`review`)

Run at the end of the week (Friday or whenever the week closes).

**Steps:**

1. Read the week plan from `plans/week-YYYY-WW.md` and all daily plan files for the week (`plans/YYYY-MM-DD.md` × 5).

2. Tally: how many PRIORITIZE tasks were completed vs missed, and same for HIGH and STANDARD.

3. List all incomplete tasks and ask the user: carry forward to next week, drop, or reschedule?

4. If `habits/habits.md` exists, summarize the week's streaks — which habits held all week, which broke, and the current streak count for each.

5. If `journal/YYYY-MM.md` exists, read this week's entries (by date) and write a short 1–2 sentence synthesis of the week's reflections — pull from what the user actually wrote, don't invent a theme that isn't there. If there are no entries for the week, omit this line rather than forcing one.

6. Ask for a one-sentence reflection on the week.

7. Display the weekly review summary using the template below.

8. Offer to immediately start next week's planning session (`/routine-goals week`).

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

## 🔥 Habit Streaks
- [habit] — [N]-day streak, held all week
- [habit] — broke on [day], now at [N]
(omit this section if habits/habits.md doesn't exist)

## 🌟 Week in Reflection
[1–2 sentence synthesis pulled from this week's journal entries]
(omit this section if journal/YYYY-MM.md has no entries for this week)

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

2. Ask what the user wants to change: add a task, complete a task, remove a task, change a priority, add or update a time estimate, add or update a deadline, add or update a `[goal: M#/Q#]` tag, or update the Recurring Meetings section.

3. Apply the changes and save the updated file with a new `Last updated` timestamp.

4. Confirm the update and ask if they'd like to regenerate today's day plan with the changes applied.

---

## Mode 6: Quick Add (`add <task>`)

Add a single task to the week plan without going through the full update flow.

**Steps:**

1. Parse `$ARGUMENTS` — everything after `add` is the task description. Look for a priority keyword (PRIORITIZE, HIGH, STANDARD, QUICK WIN), time estimate (`[Xh]`/`[Xm]`), deadline (`[due: YYYY-MM-DD]`), and goal tag (`[goal: M#]` or `[goal: Q#]`) in the text.

2. If no priority keyword is found, default to STANDARD.

3. Append the task to the correct section in `plans/week-YYYY-WW.md` and update the `Last updated` timestamp.

4. Confirm: "Added '[task]' to [tier] for this week."

---

## Mode 7: Goals (`goals`)

Set up or update monthly and quarterly goals.

**Steps:**

1. Check if `goals/quarterly.md` and `goals/monthly.md` exist. If both are missing, start fresh. If they exist, display the current goals and ask what to update.

2. Ask for quarterly goals (3–5 maximum):
   - What are you trying to achieve this quarter?
   - Label each with an ID: Q1, Q2, Q3, etc.

3. Ask for monthly goals for the current month (2–4 maximum):
   - What are this month's milestones that serve the quarterly goals?
   - Each monthly goal should reference a quarterly goal ID (e.g., `→ Q1`).

4. If `goals/` does not exist, create it. Save to `goals/quarterly.md` and `goals/monthly.md` using the templates below.

5. Mention that tasks in the week plan can be linked to a goal with a `[goal: M1]` or `[goal: Q1]` tag (see task format below) — the daily plan and weekly review both surface progress based on this tag. This is a reminder only; don't require it.

**Quarterly goals template (`goals/quarterly.md`):**

```markdown
# 🎯 Quarterly Goals — [Q1/Q2/Q3/Q4 YYYY]

- **Q1:** [goal]
- **Q2:** [goal]
- **Q3:** [goal]

_Updated: YYYY-MM-DD_
```

**Monthly goals template (`goals/monthly.md`):**

```markdown
# 📅 Monthly Goals — [Month YYYY]

- **M1:** [goal] → Q[N]
- **M2:** [goal] → Q[N]
- **M3:** [goal] → Q[N]

_Updated: YYYY-MM-DD_
```

---

## Mode 8: Habits (`habits`)

Set up or update recurring habits — small, repeatable commitments tracked with a streak, optionally tied to a goal.

**Steps:**

1. Check if `habits/habits.md` exists. If it exists, display the current habit list with streaks and ask what to change: add a habit, remove a habit, or edit a habit's frequency or linked goal.

2. For a new habit, ask:
   - What's the habit? (short name, e.g. "Read Spanish 20 min")
   - How often? Daily, or specific weekdays (e.g. "Mon, Wed, Fri")
   - Does this serve a goal? (optional — reference an existing `M#` or `Q#` from `goals/monthly.md` / `goals/quarterly.md`, or "no goal")

3. New habits start at a 0-day streak with no `Last Check-in` date.

4. If `habits/` does not exist, create it. Save to `habits/habits.md` using the template below.

5. Confirm what was added/changed.

**Habits template (`habits/habits.md`):**

```markdown
# 🔥 Habits

| Habit | Frequency | Linked Goal | Streak | Last Check-in |
|-------|-----------|-------------|--------|----------------|
| [habit] | [Daily / Mon,Wed,Fri] | [M1 / Q1 / —] | [N] days | [YYYY-MM-DD or —] |

_Updated: YYYY-MM-DD_
```

---

## Notes

- Always read the existing week plan file before generating a day plan — never make up tasks.
- If `plans/` does not exist, create it before writing the first file. If `goals/`, `habits/`, or `journal/` don't exist, create them the first time their respective mode runs.
- `plans/`, `goals/`, `habits/`, and `journal/` are all gitignored — never remove those entries, and never suggest committing their contents.
- Never delete a week plan file — completed tasks stay in the file marked `[x]` for the weekly review. Never delete or overwrite a past entry in `journal/YYYY-MM.md` — it's append-only.
- If the user's daily total exceeds ~7 hours of estimated tasks (excluding meetings), flag the overload and suggest deferring lowest-priority items rather than silently cutting them.
- QUICK WIN tasks should not anchor a time block — surface them as a separate list for opportunistic moments between tasks.
- Recurring tasks always appear regardless of the day, unless the user says a specific one doesn't apply today.
- Deadline format: `[due: YYYY-MM-DD]`. Flag any task due today or within 2 days as a ⚠️ warning at the top of the daily plan.
- Goal tag format: `[goal: M1]` or `[goal: Q1]`, optional on any task. Used to connect today's work back to a goal in the daily plan and weekly review — never required, and never fabricate a connection that isn't tagged.
- Habit streak logic: a streak only changes during `/routine-goals recap` — if the user skips recap for a day, the streak doesn't silently break; it just isn't updated until the next recap, at which point the user reports what actually happened. Never infer a missed day on your own.
- Weekend grace: Saturdays and Sundays are a no-penalty zone for every habit, regardless of what's in its `Frequency` column — doing the habit still grows the streak, but skipping it on a weekend never resets one. This applies globally (not per-habit); weekdays keep the strict reset-on-miss rule.
- Reflection prompts in recap are always optional — if the user has nothing to say, write "—" rather than manufacturing an answer, and never skip logging the entry entirely just because both prompts were empty.
- Meeting format in week plan: `[Meeting name] — [Day] [HH:MM AM – HH:MM AM]`. One-off meetings are entered during daily plan generation and are not saved to the week file.
