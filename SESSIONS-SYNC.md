# Syncing SESSIONS.md to Asana

This is the procedure a Claude Code session follows when Whitney says "sync to
Asana", at the start of a day, or before a meeting. It is on demand only; there is
no background job. It doubles as the park point before she steps away.

The goal: every live thread shows up as one card on its Initiative board, and her
"Waiting on Whitney" cards land in her My Tasks. Re-running this updates cards in
place. It never creates duplicates.

## What you need

- The Asana connector must be active in the session (tools named
  `mcp__...asana...`). If it is not, tell Whitney it needs connecting and stop.
- `.sessions-asana-map.json` in the repo root. It holds:
  - `boards`: each Initiative name to its board GID and five section GIDs.
  - `threads`: each stable Thread id to its Asana task GID.
  - `assignee_account.gid`: Whitney's real account, for "Waiting on Whitney" cards.

## The routing rule

A thread files under its `Initiative`, not its repo. Read the `Initiative:` line in
each block and look that name up in `boards`. If the Initiative is not a board,
use the `To be filed` board. The repo and branch go on the card as a tag line,
they do not decide the board.

## Status to section, and who gets assigned

| SESSIONS.md status | Board section          | Assignee                       |
|--------------------|------------------------|--------------------------------|
| RUNNING            | Claude Code is doing   | Claudsulis (acting_account.gid)|
| WAITING ON WHITNEY     | Waiting on Whitney         | Whitney (assignee_account.gid) |
| BLOCKED            | Blocked                | Claudsulis (acting_account.gid)|
| DONE               | Done (mark completed)  | unassigned                     |

The rule: Claude Code's own work (running, queued in Up next, or blocked) is
assigned to the Claudsulis acting account, and only "Waiting on Whitney" cards are
assigned to Whitney. That way her My Tasks stays an accurate list of what she
actually owes, and the Claudsulis tasks show what Claude Code owns.

## The card body (html_notes), keep it short

```
Goal: <one line>
Repo and branch: <repo>, branch <branch>
Claude Code just did: <one line>
Your next move: <the single thing she must do; if it needs the terminal, include
the exact command and one plain-language line above it>
Open issues: <real blockers or decisions only, or none>
Updated: <date and time>
```

## Bundles and subtasks

If a thread is really several related items, make it ONE parent card and add each
item as a subtask (set the `parent` field to the card's GID on `create_tasks`),
rather than one long body or many separate cards. The board face stays a clean
list of threads; the detail lives in subtasks. Keep assignment on the parent
(Claudsulis for Claude work, Whitney for Waiting on Whitney) and leave subtasks
unassigned, so My Tasks does not fill up with subtasks.

## The steps (idempotent)

For each block in `SESSIONS.md`:

1. Read its `Thread id`, `Initiative`, status tag, and body.
2. Resolve the board GID and the target section GID from `boards` using the
   Initiative and the status-to-section table.
3. Look up the Thread id in `threads`.
   - If it is there, call `update_tasks` on that task GID: set the name, set
     `html_notes` to the card body, move it with `add_projects`
     `[{project_id, section_id}]`, set `completed` true for DONE, and set
     `assignee` per the table: Whitney's gid for WAITING ON WHITNEY, the Claudsulis
     acting_account.gid for RUNNING and BLOCKED (and queued Up next), `null` for DONE.
   - If it is not there, call `create_tasks` on the board and section, then write
     the returned task GID back into `threads` in `.sessions-asana-map.json` so
     the next sync updates instead of duplicating.
4. Never change a Thread id. It is the key that prevents duplicates.

When done, tell Whitney what changed in one short summary, and give her the board
links for anything now waiting on her.

## Rolling this out to other repos

Copy `SESSIONS.md`, the "Keep SESSIONS.md current" block in `CLAUDE.md`,
`.sessions-asana-map.json` (the `boards` registry is the same everywhere; start
each repo's `threads` empty), and this file into each repo. The board registry is
shared; only the `threads` map is per repo.
