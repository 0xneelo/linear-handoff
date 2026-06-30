---
name: linear-handoff
description: Use when handing off remaining or blocked work to a future agent or session and a throwaway temp note isn't enough — the handoff must persist and stay findable, tracked in Linear. Triggers: invoking /linear-handoff, closing a session with open blockers, delegating a project's remaining work, any handoff that must survive the chat (not get lost in /tmp), or when the next agent must claim and start the work from a single pasted prompt with zero chat context.
---

# Linear Handoff

## Overview
A **durable** handoff: it writes the handoff doc, registers it, and links it to Linear so a future agent or human can resume from the Linear issue alone — plus a **kickoff prompt** the next agent pastes to *pull* (claim) and *start* the work cold. Plain `handoff` only writes a temp file; this closes the loop to Linear and to the next agent.

## When to use
- Handing off remaining or blocked work so another agent/session can finish it.
- Closing a session whose work outlives the conversation.
- Any time a `/tmp` note alone would get lost — you want it tracked and findable.
- Use plain `handoff` instead for a throwaway note when Linear isn't wanted or available.

## Procedure
1. **Write the handoff doc** to the OS temp dir (Linux/WSL `/tmp`, Windows `%TEMP%`), named `handoff-<slug>-<YYYYMMDD>.md`. Cover: Mission · Current state · Next steps (ordered) · Key context/gotchas · Open questions/blockers · References · Suggested skills. Dense and high-signal; **reference artifacts (issues, commits, files) by id/path — don't duplicate them**.
2. **Register it.** Append a newest-first entry to the project handoff register (`_local/handoff-register.md` if present, else create it), matching the existing format, with: Created · Handoff path · Focus · Summary · Receiving agent · a **`Linear:`** field. **If the register's folder or any parent dir doesn't exist, create it first** — don't let the write fail on a missing folder.
3. **Link to Linear.** Post a SUMMARY (state, next step, blockers, gotchas — not a copy of the doc) as a comment on the most relevant issue/epic, or create a "Session handoff" issue. Fill the register's `Linear:` field with that issue id/URL.
4. **Write the kickoff prompt** (below) and place the SAME block on three channels so it's reachable however the next operator arrives:
   - **Linear (durable):** append it as a fenced `text` block at the END of the step-3 issue's description — the litmus depends on this copy.
   - **Temp doc (full record):** mirror it as the last section of the step-1 doc.
   - **Chat (immediate):** print it in step 5.
5. **Tell the user** the temp path, the register entry, and the Linear link — **and print the kickoff prompt as a fenced code block in the chat** so the operator can paste it into a fresh agent. Never auto-send it into the current session (that starts the task here instead of in a fresh agent).

## Kickoff prompt
A **self-contained pointer block** a zero-context agent pastes to claim and start: it points at the doc/register/issue by id/path and never reproduces them. Required parts, in order: **role/mission · pull-the-task · read-context · first-skill · first-action · guardrails · close-the-loop** — shaped like:
```text
You are <role>. Pull and start Linear issue <ID> ("<title>").

1. PULL THE TASK: assign <ID> to yourself and set it "In Progress" so it isn't double-grabbed.
2. READ CONTEXT: the brief at <doc path> and the entry in <register path>; the issue is <ID>.
3. INVOKE `<skill>` FIRST (it governs this work).
4. DO: <first concrete action(s)>.
5. GOTCHAS: <one-line pointer to the doc's gotchas/blockers>.
6. CLOSE THE LOOP: when done, comment on <ID> with what changed + evidence and set it Done;
   if blocked, comment the blocker and leave it In Progress.
```

## Rules
- **Redact secrets** — never put API keys, tokens, passwords, or PII in the doc, register, Linear, or kickoff prompt.
- The Linear comment and the kickoff prompt both **point** back to the doc — neither reproduces it. If Linear filing is blocked (e.g., an approval gate), record that in the `Linear:` field and still write the doc + register entry + kickoff prompt.

## Verification
A correct run leaves **three linked artifacts** — the temp doc, a register entry whose `Linear:` field is filled, and a Linear comment/issue that points back — **plus a kickoff prompt embedded in both the Linear issue description and the temp doc, and surfaced in the chat**. Litmus: a fresh, zero-context agent can resume from the Linear issue alone, and the operator can paste the chat block to boot a new agent.
