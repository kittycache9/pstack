# pstack in opencode

## Install

Copy or symlink each folder under `<pstack>/skills/` into one of the directories opencode scans:

- Project: `.opencode/skills/`, `.agents/skills/`, or `.claude/skills/`. opencode searches from the current directory up to the project root.
- User: `~/.config/opencode/skills/`, `~/.agents/skills/`, or `~/.claude/skills/`

You can also list extra local directories or HTTP catalogs in the `skills` array of `opencode.json`. The agent loads a skill with the `skill` tool.

## Agent profiles

Custom agents are Markdown files in `.opencode/agents/<name>.md` or `~/.config/opencode/agents/<name>.md`. The body is the system prompt. To install `poteto-agent` or Comment Sicko, copy `agents/<name>.md` there and set `mode: subagent` in its frontmatter. Remove frontmatter fields opencode doesn't know, such as `is_background`. Type `@<name>` to call a subagent directly.

## Terms

| Neutral term | opencode |
| --- | --- |
| subagent tool | `subagent` in current docs. Older versions call it `task` (**unverified**). Subagents run in child sessions, and the parent agent's `subagent` permission rules decide which it may launch. |
| a general-purpose subagent | the built-in `general` subagent (**unverified**) |
| a read-only subagent | An agent whose `permissions` deny edits, or a brief that says "do not edit files" |
| a remote worker or isolated worker | None built in. Use a fresh worktree. |
| structured question tool | A question tool when the session lists one (**unverified**). Otherwise ask in plain text. |
| todo list | `todowrite` when the session lists it (**unverified**) |
| a persistent mode | A primary agent (`mode: primary`) whose prompt says to follow `poteto-mode`. Switch to it for the session. |
| recurring-run or scheduled automation | None known. Use a bounded poll loop, or run `opencode run` from CI. |

## Models

A model value is `provider/model`, optionally followed by `#variant`, such as `anthropic/<model>#high`. An agent file's `model` field sets that agent's model. Whether a subagent spawn takes a per-call model depends on the version, so `/setup-pstack` should detect it from the tool's fields, and otherwise fall back to the session model.

Skills read `~/.pstack/models.md` on demand. To preload it, mirror it into `~/.config/opencode/AGENTS.md`, or add its path to the `instructions` array in `opencode.json` (**unverified**).

## Paths

| What | Where |
| --- | --- |
| Always-applied instructions | `AGENTS.md` in the project, `~/.config/opencode/AGENTS.md`, and files listed under `instructions` in `opencode.json` (**unverified**) |
| Config | `opencode.json` or `opencode.jsonc` in the project, `~/.config/opencode/` |
| Session transcripts | Stored as JSON under `~/.local/share/opencode/storage/` (**unverified**). `opencode export <session-id>` writes one session as JSON (**unverified**). Read only this project's sessions. |
