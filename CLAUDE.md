# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

This repository is a collection of custom Claude Code skills (slash commands). Skills live in `.claude/skills/` and are available in any Claude Code session opened from this directory.

## Structure

```
.claude/
  skills/
    <skill-name>/
      SKILL.md        # Required — main instructions and frontmatter
      reference.md    # Optional — detailed reference loaded on demand
      scripts/        # Optional — helper scripts
  settings.local.json
CLAUDE.md
```

## Adding a Skill

Create `.claude/skills/<skill-name>/SKILL.md`. The directory name becomes the `/slash-command`. Skills must include YAML frontmatter with at least a `description` field.

Minimal template:
```yaml
---
name: skill-name
description: Use when someone asks to [action] or [action].
---

## Steps
1. ...
```

## Active Skills

| Skill | Command | Description |
|-------|---------|-------------|
| skill-builder | `/skill-builder` | Build, optimize, or audit skills using the Discovery Interview process |
| tech-update | `/tech-update` | Latest news in AI, tech, and the industry — accepts an optional topic or company name |
| routine-goals | `/routine-goals` | Plan your day/week, manage priorities, deadlines, meetings, and goals; track habit streaks and journal daily reflections — saves to `plans/`, `goals/`, `habits/`, `journal/` (all gitignored) |
| market-brief | `/market-brief` | Portfolio/watchlist tracker + market news brief, with screenshot import — saves to `finance/` (gitignored) |
| morning-coffee | `/morning-coffee` | World news briefing — politics, economies, geopolitics; tiered country coverage + a personal follow list — saves to `world/` |
| yt-ideas | `/yt-ideas` | Anime YouTube video ideas from current buzz + channel/competitor research; `develop <idea>` drafts a hook + outline — saves to `content/` (gitignored) |
| greet | `/greet` | Greet a user by name |

## Key Conventions

- `description` is written as: "Use when someone asks to [action], [action], or [action]."
- Set `disable-model-invocation: true` on skills with side effects (file generation, API calls).
- Keep SKILL.md under 500 lines — move reference material to supporting files in the same directory.
- Use `$ARGUMENTS` / `$0`, `$1` for user-supplied input.
- Set `context: fork` + `agent:` for self-contained tasks that should run in an isolated subagent.
