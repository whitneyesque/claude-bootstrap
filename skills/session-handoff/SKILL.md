---
name: session-handoff
description: At the end of a working session, write a timestamped handoff document (what happened, what was done, what was learned, what did not work, what is left, how long it took, who was consulted, what tools were used) and append a matching entry to your AI diary. Works with Claude Code and other agent tools such as Antigravity. Generic and shareable; edit the one-time setup block before first use.
---

# Session handoff and AI diary

This skill does two things at the end of a working session:

1. Writes a **timestamped handoff file** a fresh session can read to pick up where this one left off.
2. Appends a short **entry to your AI diary** (a running log of your AI work and learnings).

It is written to be shared. Before the first run, fill in the one-time setup block below. After that, just invoke it at the end of a session.

---

## One-time setup (edit these two lines, then leave them)

- **YOUR NAME:** `FILL IN` (used to title the diary entry, for example "Sam")
- **YOUR AI DIARY FILE:** `FILL IN` (absolute path to your AI diary markdown file)
  - If your diary lives in a Google Drive that syncs to your computer, the path usually looks like:
    `~/Library/CloudStorage/GoogleDrive-you@example.com/Shared drives/<folder path>/Your Name's AI Diary.md` (Mac)
    or `~/Google Drive/<folder path>/Your Name's AI Diary.md`.
  - If you do not have a diary file yet, create an empty one at that path with a title line, or tell the skill to create it on first run.

When the skill runs, it reads these two values from this block. If either still says `FILL IN`, do the handoff file anyway and print the diary entry in the chat for the person to paste, telling them to set the path.

---

## Step 1: Find the project root and timestamp

1. Get the project root: run `git rev-parse --show-toplevel`. If that fails (not a git repo), use the current working directory.
2. Get the timestamp string: run `date +%Y-%m-%d-%H%M`. Use that exact string below.

## Step 2: Write the handoff file

Save it at:
```
<project-root>/session-handoffs/SESSION-HANDOFF-<timestamp>.md
```
Create the `session-handoffs/` folder if it does not exist.

## Step 3: Gather the state (so the next session is not guessing)

If this is a git repo, capture the live state and use it in the handoff. Run and read:
- `git branch --show-current`
- `git status --short`  (uncommitted and untracked files)
- `git log --oneline -5`
- `git diff --name-status main...HEAD` (files changed vs the main branch, if there is one)
- open pull requests, if the tool has a CLI for it (for example `gh pr list` for GitHub)

If any command fails, note that in the handoff rather than leaving a blank. If it is not a git repo, just describe what files or systems changed.

## Step 3b: Measure how long it took, from the tool's log (never from memory)

You cannot feel elapsed time, so do not estimate it. Read real timestamps from the agent tool's own session log. Report a **total span** and an **approximate per-task breakdown** (one line per request or chapter), and tag the whole thing "approx" because wall-clock time includes thinking and time away.

**If you are running in Claude Code:** the session is logged to a JSONL transcript where every event has an ISO-8601 `timestamp`. This finds the active transcript and prints the total span plus a per-user-turn breakdown:

```bash
ROOT=$(git rev-parse --show-toplevel 2>/dev/null || pwd)
SLUG=$(echo "$ROOT" | sed 's#/#-#g')
F=$(ls -t "$HOME/.claude/projects/$SLUG/"*.jsonl 2>/dev/null | head -1)
python3 - "$F" <<'PY'
import sys, json, datetime
f = sys.argv[1] if len(sys.argv) > 1 else ""
if not f:
    print("timing unavailable (no transcript found)"); raise SystemExit
def parse(x): return datetime.datetime.fromisoformat(x.replace('Z', '+00:00'))
ts, turns = [], []
for ln in open(f):
    try: o = json.loads(ln)
    except: continue
    t = o.get('timestamp')
    if not t: continue
    ts.append(t)
    if o.get('type') == 'user':
        c = (o.get('message') or {}).get('content')
        text = c if isinstance(c, str) else None
        if isinstance(c, list):
            parts = [p.get('text') for p in c if isinstance(p, dict) and p.get('type') == 'text']
            text = ' '.join(p for p in parts if p) if parts else None
        if text: turns.append((t, ' '.join(text.split())[:60]))
if not ts:
    print("timing unavailable (no timestamps in transcript)"); raise SystemExit
print("TOTAL span:", str(parse(max(ts)) - parse(min(ts))).split('.')[0])
end = parse(max(ts)); turns.sort()
for i, (t, label) in enumerate(turns):
    nxt = parse(turns[i+1][0]) if i+1 < len(turns) else end
    print(f"  {str(nxt - parse(t)).split('.')[0]:>10}  {label}")
PY
```

**If you are running in Antigravity (or another agent tool):** it keeps its own session or event log with timestamps too, just in a different place and format. Find that tool's session log for the current session, read the first and last event timestamps for the total span, and use the per-step or per-message timestamps for the approximate breakdown. The same rule holds: read real timestamps, do not invent them.

**If you cannot read any log:** write "timing unavailable" in the handoff. Never backfill with a guess. Field names also shift between tool versions; if the per-task breakdown looks empty or wrong, report only the reliable total span.

## Step 4: Write the content

Use this structure. Be specific: real file paths, branch names, commit ids, links, names. A skim-only reader should be able to reconstruct the session from the headings.

```markdown
# Session handoff — <human-readable date and time>

## What this session was about
One or two sentences on the goal or theme.

## How long it took (approx, from the session log)
Total span, then a per-task breakdown (one line per request or chapter) from Step 3b. Tag it "approx". If the log could not be read, write "timing unavailable".

## What was done
Concrete things accomplished. For each: what changed, why, and where it lives now (path, branch, link).

## What was learned
Durable, transferable learnings, not just a task list. The things future-you would want to know.

## What did not work (dead ends)
Things tried that failed. One line each: what was tried, why it failed, what would have to change to make it worth trying again. If there were none, say so.

## What is left to do
A numbered list of unfinished items. For each: what it is, what is blocking it, and the exact next step.

## Who we had to go to
People and approvals this work depended on: a decision asked of the user, an admin or teammate, an external support contact, a review still owed. Note what is still waiting on someone.

## What other tools were used
Tools and services beyond the agent itself: connectors or MCP tools, CLIs, APIs, external apps, dashboards. Note anything that needed setup, a key, or a login, so the next session is not surprised.

## How to pick this up
A short paragraph: what a fresh session should read or open first to get oriented.
```

## Step 5: Keep one source of truth

After writing, glance at the project root for older undated handoff files (for example `HANDOFF.md`, `NEXT-SESSION.md`). If one is clearly superseded by this dated handoff, offer to remove it. If unsure, leave it and mention it. The dated files in `session-handoffs/` are the canonical record.

## Step 6: Append the entry to the AI diary

In addition to the handoff file, append a short entry to the AI diary file from the setup block.

1. Read the **YOUR AI DIARY FILE** path. If it still says `FILL IN` or the file cannot be reached (no Drive mount, different machine, sandboxed), do not skip: print the diary entry in the chat and tell the person to paste it in, noting the path was not reachable.
2. If the file exists, **append the new entry at the TOP** (newest first), just below any title or intro and above the previous entry. Do not create a second file.
3. Date the entry, title it with **YOUR NAME** and a short topic, and use this shape:
   - **What this session was.**
   - **What got done.**
   - **Learnings worth keeping.** (favor the transferable insight)
   - **Where things stand.**
4. Keep it readable on its own; it may be read by a person or ingested into a tool like NotebookLM later. Plain punctuation travels best.

To insert at the top without rewriting the whole file by hand, read it, place the new entry before the first existing entry heading, and write it back. For example:
```bash
python3 - "$DIARY_PATH" "$ENTRY_FILE" <<'PY'
import sys
diary, entry = sys.argv[1], sys.argv[2]
lines = open(diary, encoding='utf-8').read().splitlines(keepends=True)
new = open(entry, encoding='utf-8').read().rstrip() + "\n\n"
idx = next((i for i, l in enumerate(lines) if l.startswith('## ')), len(lines))
open(diary, 'w', encoding='utf-8').write(''.join(lines[:idx] + [new] + lines[idx:]))
print("Inserted entry before line", idx + 1)
PY
```
(Write your entry to a temp file first, then run this with the diary path and the temp file path.)

## Step 7: Report

Print: the handoff file path, a one-line summary of what is in it, anything removed in Step 5, and whether the diary entry was appended or printed for paste (and why, if printed).

## Notes for whoever installs this

- Drop the `session-handoff/` folder into your agent's skills directory (for Claude Code that is `~/.claude/skills/` or a project's `.claude/skills/`), then invoke it by name at the end of a session.
- Edit the one-time setup block first. Everything else is generic.
- If your agent already has a skill named `session-handoff`, rename this folder to avoid a clash.
