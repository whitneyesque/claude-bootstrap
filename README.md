# claude-bootstrap

Drop this folder's contents into the root of any new project to give Claude Code the working rules Whitney expects. Then edit `CLAUDE.md` to fill in the project-specific sections.

## What's in here

- **`CLAUDE.md`** — the project rules template. Top half is Whitney's standing preferences (brain fog, no em dashes, build/deploy cadence). Bottom half is placeholder sections for the new project's stack, repo orientation, and deploy steps.

## How to use

1. Copy `CLAUDE.md` into the new repo's root.
2. Open it and fill in the `<<FILL IN>>` placeholders.
3. Commit it to the repo so any session in there picks it up.

That's the whole setup for a new project on this Mac. Everything else (slash commands, skills, auto-memory) lives in `~/.claude/` and applies globally.

## Files outside this folder that also matter

These live in `~/.claude/` and apply to every project automatically. If you ever move to a new Mac, copy them over:

- `~/.claude/settings.json` — global Claude settings
- `~/.claude/commands/brainfog.md` — `/brainfog` slash command
- `~/.claude/commands/mobile.md` — `/mobile` slash command
- `~/.claude/skills/case-reviewer/` — `/case-reviewer` skill
- `~/.claude/projects/<project-slug>/memory/` — per-project auto-memory (regenerates per project)

Skip when copying: `sessions/`, `telemetry/`, `cache/`, `backups/` — runtime junk.
