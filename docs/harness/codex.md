# pstack in Codex

## Install

Codex reads skills only from the shared directory. Copy or symlink each folder under `<pstack>/skills/` into one of these:

- Project: `.agents/skills/`. Codex scans it in the launch directory, the folders above it, and the repository root.
- User: `~/.agents/skills/`
- Admin: `/etc/codex/skills/`

Run `/skills` to list them, or type `$` and the skill name, such as `$poteto-mode`, to invoke one. Older Codex versions read `.codex/skills/` and `~/.codex/skills/` instead (**unverified** for current versions).

## Agent profiles

Codex custom agents are TOML files in `.codex/agents/` or `~/.codex/agents/`, one agent per file, with `name`, `description`, and `developer_instructions` fields. pstack ships its profiles as Markdown, so to install `poteto-agent` or Comment Sicko, write a TOML file per profile: copy `name` and `description` from the frontmatter and put the body in `developer_instructions`. Without that step, paste `agents/<name>.md` into the subagent brief.

Codex also has built-in agents: `default` (general purpose), `worker` (implementation), and `explorer` (read-heavy exploration). A custom agent with a built-in's name replaces it.

## Terms

| Neutral term | Codex |
| --- | --- |
| subagent tool | The multi-agent spawn tool. Its name and fields vary by version (third-party notes call it `spawn_agent`, with `agent_type` and a prompt), so use what the session lists. **Unverified.** |
| a general-purpose subagent | `default` |
| a read-only subagent | `explorer`, and the brief still says "do not edit files" |
| a remote worker or isolated worker | A fresh worktree on this machine, unless you run the task in Codex's cloud environment. **Unverified** as a subagent target. |
| structured question tool | None known. Ask in plain text. |
| todo list | The plan tool, when the session lists one (`update_plan` in versions we've seen; **unverified**). Otherwise keep the list in the reply. |
| a persistent mode | None. Name the skill again when it fades, or add a line to `AGENTS.md`. |
| recurring-run or scheduled automation | None known. Use a bounded poll loop, or run the prompt from CI with `codex exec`. |
| the repository's automated review bot | Skip unless the repo has one. Its comments are ordinary review threads. |

Concurrency limits live in config, such as `[agents] max_threads` and `max_depth` in `.codex/config.toml`.

## Models

The session model and reasoning effort are set in `~/.codex/config.toml` (`model`, `model_reasoning_effort`) or with `--model` on the command line. Whether a subagent spawn accepts its own model value depends on the version, so `/setup-pstack` should detect it from the spawn tool's fields. If the field is absent, every role runs on the session model, which is what `auto` does.

Skills read `~/.pstack/models.md` on demand. To preload it, mirror the role lines into `~/.codex/AGENTS.md`, or the repo's `AGENTS.md` if the whole team shares the config.

## Paths

| What | Where |
| --- | --- |
| Always-applied instructions | `AGENTS.md` in the repo, and `~/.codex/AGENTS.md` |
| Config | `~/.codex/config.toml`, `.codex/config.toml` |
| Session transcripts | `~/.codex/sessions/<yyyy>/<mm>/<dd>/rollout-*.jsonl` (**unverified**). Match the session to this workspace by the working directory it records, and read only that one. |
