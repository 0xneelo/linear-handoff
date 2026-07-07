# linear-handoff

A global [Claude Code](https://claude.com/claude-code) / agent **skill** for durable, Linear-tracked handoffs.

When you hand off remaining or blocked work, it writes a handoff doc, registers it in the project's handoff register, links it to Linear, and emits a self-contained **kickoff prompt** that a zero-context agent can paste to *claim* and *start* the work cold — on three channels (Linear issue description, temp doc, chat).

The skill itself is [`SKILL.md`](SKILL.md).

## Install

Clone (or symlink) into your agent's skills directory:

```sh
git clone https://github.com/0xneelo/linear-handoff.git ~/.claude/skills/linear-handoff
```

## History

- **Initial** — durable handoff: temp doc + register + Linear link.
- **SYN-233** — added the copy-pasteable, three-channel kickoff prompt (~500 → ~1110 words).
- **SYN-234** — trimmed back toward concise (~1110 → ~772 words) with no loss of load-bearing behavior, re-verified via subagent harness.
- **Cost-aware** — injected the cost stack: the kickoff now propagates model-tiering to the agent it boots (Haiku `reader` for reads, Sonnet `builder` for edits, Opus for decisions), added a run-cheap `## Cost` directive (delegate only genuine state reconstruction to a `reader`; keep Linear writes / register append / secret redaction exact), and generalized the Linear abort path (blocked → blocked-or-fails). Verified new-vs-snapshot: cost-aware kickoff emitted only by the new version, zero secret leaks, no regression.
