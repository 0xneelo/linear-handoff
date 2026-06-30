---
name: linear-handoff
description: Use when handing off remaining work to a future agent or session and a throwaway temp note is not enough — you want the handoff to persist and stay findable later, tracked in Linear. Triggers: invoking /linear-handoff, closing a session with open blockers, delegating a project's remaining/blocked work, or any handoff that must survive the chat (not get lost in /tmp).
---

# Linear Handoff

## Overview
A handoff that is also **durable**: it writes the handoff document, registers it in the project's handoff register, and links it to Linear so a future agent or human can resume from the Linear issue alone. The plain `handoff` skill only writes a temp file; this one closes the loop to Linear.

## When to use
- Handing off remaining or blocked work so another agent/session can finish it.
- Closing a session whose work outlives the conversation.
- Any time a `/tmp` handoff alone would get lost — you want it tracked and findable.
- Use plain `handoff` instead when you only need a throwaway note and Linear isn't wanted or available.

## Procedure
1. **Write the handoff doc** to the OS temp dir (Linux/WSL `/tmp`, Windows `%TEMP%`), filename `handoff-<slug>-<YYYYMMDD>.md`. Cover: Mission · Current state · Next steps (ordered) · Key context/gotchas · Open questions/blockers · References · Suggested skills. Dense and high-signal; **reference artifacts (issues, commits, files) by id/path — do NOT duplicate them**.
2. **Register it.** Append a newest-first entry to the project handoff register (`_local/handoff-register.md` if present; otherwise create it), matching the existing entry format, with: Created · Handoff path · Focus · Summary · Receiving agent · a **`Linear:`** field.
3. **Link to Linear.** Post a SUMMARY (state, immediate next step, blockers, gotchas — not a copy of the doc) as a comment on the most relevant Linear issue/epic, or create a dedicated "Session handoff" issue. Then fill the register's `Linear:` field with that issue id/URL.
4. **Tell the user** all three: the temp path, the register entry, and the Linear link.

## Rules
- **Redact secrets** — never put API keys, tokens, passwords, or PII in the temp doc, the register, or Linear.
- The Linear comment points back to the doc; it does not reproduce it.
- If Linear filing is genuinely blocked (e.g., an open approval gate), record that blocker in the `Linear:` field and still write the doc + register entry — don't silently skip the linkage.

## Verification
A correct run leaves **three linked artifacts**: the temp doc, a register entry whose `Linear:` field is filled, and a Linear comment/issue that points back. Litmus test: a fresh agent can resume from the Linear issue alone.

## Common mistakes
- Writing only the temp doc (the `handoff` failure mode) — the whole point here is the register + Linear link.
- Dumping the full doc into Linear instead of a pointer summary.
- Hardcoding a team/project — discover the relevant issue from context, or ask which issue/project to attach to.
