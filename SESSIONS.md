# Sessions board — claude-bootstrap

Live status of every Claude Code session in this repo. Each session updates its
own block at the end of each turn. This is the quick board, not the deep record.
Full end-of-session detail still goes in session-handoffs/ as usual.

STATUS TAGS:   RUNNING   ·   WAITING ON WHITNEY   ·   BLOCKED   ·   DONE

To push these blocks to Asana, see SESSIONS-SYNC.md. The thread-id to card
mapping is in .sessions-asana-map.json, so re-syncing updates cards instead of
duplicating them.

------------------------------------------------------------

No active sessions logged yet. When a session starts a thread, it adds a block
here by copying the format below, gives it a stable Thread id and an Initiative,
then syncs. If the thread already has an Asana card, set its Thread id in
.sessions-asana-map.json to that card's GID so the sync updates it instead of
making a duplicate.

## <thread name> — WAITING ON WHITNEY
Thread id: <stable-slug-that-never-changes>
Initiative: <the theme this belongs to>
Branch / worktree: <branch, or main>

Claude Code just did: <one line>

>> YOUR NEXT MOVE: <the single thing she has to do, plain words>

    # <one plain-language line: what this does and why>
    <exact command to copy and paste, or omit this block if no command>

Open issues: <real blockers or decisions only, or "none">
Updated: <date and time>

------------------------------------------------------------
