---
name: linear-handoff
description: Use when handing off remaining work to a future agent or session and a throwaway temp note is not enough — you want the handoff to persist and stay findable later, tracked in Linear. Triggers: invoking /linear-handoff, closing a session with open blockers, delegating a project's remaining/blocked work, any handoff that must survive the chat (not get lost in /tmp), or when the next agent must be able to claim and start the work from a single pasted prompt with zero chat context.
---

# Linear Handoff

## Overview
A handoff that is also **durable**: it writes the handoff document, registers it in the project's handoff register, and links it to Linear so a future agent or human can resume from the Linear issue alone. It also emits a **kickoff prompt** — a self-contained block the next agent pastes to *pull* (claim) and *start* the work cold. The plain `handoff` skill only writes a temp file; this one closes the loop to Linear and to the next agent.

## When to use
- Handing off remaining or blocked work so another agent/session can finish it.
- Closing a session whose work outlives the conversation.
- Any time a `/tmp` handoff alone would get lost — you want it tracked and findable.
- Use plain `handoff` instead when you only need a throwaway note and Linear isn't wanted or available.

## Procedure
1. **Write the handoff doc** to the OS temp dir (Linux/WSL `/tmp`, Windows `%TEMP%`), filename `handoff-<slug>-<YYYYMMDD>.md`. Cover: Mission · Current state · Next steps (ordered) · Key context/gotchas · Open questions/blockers · References · Suggested skills. Dense and high-signal; **reference artifacts (issues, commits, files) by id/path — do NOT duplicate them**.
2. **Register it.** Append a newest-first entry to the project handoff register (`_local/handoff-register.md` if present; otherwise create it), matching the existing entry format, with: Created · Handoff path · Focus · Summary · Receiving agent · a **`Linear:`** field. If the register's folder (e.g. `_local/`) or any parent directory doesn't exist yet, create it first — don't let the write fail on a missing folder.
3. **Link to Linear.** Post a SUMMARY (state, immediate next step, blockers, gotchas — not a copy of the doc) as a comment on the most relevant Linear issue/epic, or create a dedicated "Session handoff" issue. Then fill the register's `Linear:` field with that issue id/URL.
4. **Write the kickoff prompt** (template below) and place the SAME block on three channels, so it's reachable however the next operator arrives:
   - **Linear (durable):** append it as a fenced `text` code block at the END of the step-3 issue's description — the litmus depends on this copy.
   - **Temp doc (full record):** mirror it as the last section of the step-1 doc.
   - **Chat (immediate):** print it in step 5 (below).
5. **Tell the user** the temp path, the register entry, and the Linear link — **and print the kickoff prompt itself as a fenced code block in the chat**, so the operator can copy-paste it into a fresh agent and start immediately. Where the client renders widgets, an interactive "Copy" affordance is a nice touch; never auto-send the prompt into the current session (that would start the task here instead of in a fresh agent).

## Kickoff prompt template
A **self-contained pointer block** a zero-context agent can paste to claim and start: it points at the doc/register/issue by id/path and never reproduces them. Required parts, in order:
1. **Role/mission** — one line naming the task and its Linear id.
2. **Pull the task** — assign the issue to yourself (the pasting agent; or to a named operator/delegate if one is given) and set it **In Progress**, so it isn't double-grabbed.
3. **Read context** — the handoff doc (path), the register entry, and the issue id.
4. **First skill to invoke** — the skill that governs the work (invoke before acting).
5. **First action(s)** — the concrete next step(s) to take.
6. **Guardrails/gotchas** — one line pointing at the doc's gotchas/blockers.
7. **Close the loop** — on done: comment what changed + evidence and set the issue Done; if blocked: comment the blocker and leave it In Progress.

Shape to match (fill the slots; keep it this tight):
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
- **Redact secrets** — never put API keys, tokens, passwords, or PII in the temp doc, the register, Linear, or the kickoff prompt.
- The Linear comment and the kickoff prompt both POINT back to the doc — neither reproduces it.
- If Linear filing is genuinely blocked (e.g., an open approval gate), record that blocker in the `Linear:` field and still write the doc + register entry + kickoff prompt — don't silently skip the linkage.

## Verification
A correct run leaves **three linked artifacts** — the temp doc, a register entry whose `Linear:` field is filled, and a Linear comment/issue that points back — **plus a kickoff prompt embedded in both the Linear issue description and the temp doc, and surfaced in the chat**. Litmus: a fresh, zero-context agent can resume from the Linear issue alone, and the operator can paste the chat block to boot a new agent without hunting for context.

## Common mistakes
- Writing only the temp doc (the `handoff` failure mode) — the whole point here is the register + Linear link.
- Dumping the full doc into Linear instead of a pointer summary.
- Hardcoding a team/project — discover the relevant issue from context, or ask which issue/project to attach to.
- A vague or context-dependent kickoff prompt — it assumes the chat history, names no issue id, or omits the pull/assign + In Progress step, so a cold agent can't actually start.
- Kickoff prompt only in the temp doc — if it isn't in Linear it's unreachable without filesystem access; the durable copy must live in the issue.
- Kickoff prompt not surfaced in the chat — the operator can't copy-paste to boot the next agent and has to hand-write it.
- Pasting the whole brief/doc into the prompt instead of a self-contained pointer block.
