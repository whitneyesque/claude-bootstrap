# <<PROJECT NAME>> — instructions for Claude Code

Read this before doing anything in this repo. The rules below are Whitney's standing preferences. Project-specific rules sit at the bottom; fill them in.

---

## How Whitney works with Claude

Whitney experiences brain fog and fatigue. She is also often on her phone. This shapes how to help her:

- **Explain things clearly as you go.** Don't assume she remembers earlier context. Restate before you act.
- **Always give a clickable preview link** after a deploy. She should never have to navigate to find your work.
- **Handle all git, GitHub, terminal, and deploy work yourself.** Never say "you can push this" or "run X", just do it.
- **Step-by-step instructions** for anything she does need to do (rare). Never compound steps.
- **Deal with one issue at a time.** Don't bundle multiple fixes, don't sneak in "while I'm here" cleanups, don't ask three questions at once. Finish the current thing, ship it, then move on.
- **After any permission prompt she approves**, offer to add it to `.claude/settings.json` so she doesn't see it again. Short ask at the end of the response: "Want me to add that to settings.json so you don't see it again?"

**Why:** low cognitive load. She should not have to remember commands, navigate repos, or figure out deploy steps. You handle the technical layer entirely.

**How to apply:** after every meaningful change, build + deploy + commit, then post the preview URL.

---

## Writing rules (these apply to every file you touch)

- **No em dashes anywhere.** Use periods, semicolons, commas, or restructure. Applies to code comments, commits, PR bodies, copy, replies, everything.
- **No decoration without information.** Don't add markers, dots, badges, or numbers that don't tell the reader something useful, even if a token suggests them.
- **Headlines carry the decision.** Every section H2 must state the decision or finding outright. A skim-only reader should reconstruct the page from the headlines alone.
- **No punching down to elevate Whitney.** Never write "a junior would have X, but I…" framings.
- **First person is fine** where Whitney is the actor.

---

## Things to never do

- Don't push --force, don't reset --hard, don't skip pre-commit hooks. Investigate failures, don't bypass.
- Don't add em dashes, even sneakily.
- Don't introduce new fonts, colors, or system elements without checking the project's rules below.
- Don't fix something Whitney didn't ask you to fix as a "by the way". Flag it instead.

---

## Project-specific rules

<<FILL IN>>

---

## Repo orientation

- **Source:** `~/dev/<<PROJECT FOLDER>>/` (keep out of iCloud; iCloud breaks most file watchers).
- **Stack:** <<FILL IN: e.g. Astro 6 + Tailwind v4 + Cloudflare Workers>>
- **Key files / folders:** <<FILL IN>>

---

## Build + deploy

```
cd ~/dev/<<PROJECT FOLDER>>
<<FILL IN: build command>>
<<FILL IN: deploy command>>
```

Deploys to `<<FILL IN: preview URL>>`. Commit after deploy succeeds. Always post the URL back to Whitney.

---

## Keep SESSIONS.md current (live session board)

At the END OF EVERY TURN, update this session's block in `SESSIONS.md` at the repo
root:

- Set the STATUS TAG: RUNNING, WAITING ON YOU, BLOCKED, or DONE.
- Keep "Thread id" stable; never change it once set. Use it to find the block to
  update so you never create a duplicate.
- Set "Initiative" to the theme this thread belongs to, not the repo name. If you
  are unsure which initiative, set it to "To be filed". The current themes are:
  Health tracking (the health app and the medication tracker), Portfolio,
  Mimi Care App (personal caregiving), CareStar (work caregiving), Tooling and
  setup, Harmonic AI (work; the tracker, AI diary, AI use cases library, how-I-AI
  podcast research), and Podcast and research library (personal, health and
  comedy). Otter transcripts is its own thing, not one of the podcast libraries.
- Update "Claude Code just did" to the last thing you did, in one line.
- Update ">> YOUR NEXT MOVE" to the single next thing she has to do herself.
  - If it needs the terminal, write the EXACT command in an indented block, with
    one plain-language line above it. Assume she has barely used the terminal.
    No jargon. No em dashes.
  - If there is nothing for her to do, write "nothing, still running" or
    "nothing, done".
- Keep "Open issues" to genuine blockers or decisions only.
- Stamp "Updated" with the date and time.
- If this thread has no block yet, create one by copying the format of an
  existing block.
- Keep every block to a few lines. Full detail still goes in `session-handoffs/`.

### Syncing the board to Asana

When she says "sync to Asana" (or at the start of a day, or before a meeting),
follow the procedure in `SESSIONS-SYNC.md`. It reads each block here, routes it to
its Initiative board, and creates or updates the matching card without making
duplicates. The board and card mapping lives in `.sessions-asana-map.json`.
