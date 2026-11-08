# pstack in Gemini CLI

## Install

Copy or symlink each folder under `<pstack>/skills/` into one of the directories Gemini CLI scans:

- Workspace: `.gemini/skills/` or `.agents/skills/`
- User: `~/.gemini/skills/` or `~/.agents/skills/`

Within one tier, `.agents/skills/` wins over `.gemini/skills/`, and workspace skills win over user skills.

`gemini skills install <git-url-or-local-path> [--scope user|workspace] [--path <subdir>]` installs a skill from a repository. `--path` names the subdirectory that holds the skill, so installing all of pstack this way takes one run per skill folder. Copying the folders is simpler.

## Agent profiles

Subagents are Markdown files with YAML frontmatter in `.gemini/agents/` or `~/.gemini/agents/`. Each one is exposed to the main agent as a tool named after its `name` field. Type `@<name>` at the start of a prompt to force one. To install `poteto-agent` or Comment Sicko, copy `agents/<name>.md` there and drop frontmatter fields Gemini CLI doesn't know, such as `is_background`. Whether a hyphenated `name` is a valid tool name is **unverified**; built-in agents use underscores, such as `codebase_investigator`.

Subagents are on by default. `experimental.enableAgents: false` in `settings.json` turns them off.

## Terms

| Neutral term | Gemini CLI |
| --- | --- |
| subagent tool | Each subagent is its own tool. pstack's generic "spawn a general-purpose subagent" needs a general agent file, or runs inline. |
| a read-only subagent | An agent whose tool list leaves out the write and shell tools, or a brief that says "do not edit files" |
| a remote worker or isolated worker | None built in. Use a fresh worktree. |
| structured question tool | An ask-user tool when the session lists one (**unverified**). Otherwise ask in plain text. |
| todo list | `write_todos` when the session lists it (**unverified**) |
| a persistent mode | None. Name the skill again when it fades, or add a line to `GEMINI.md`. |
| recurring-run or scheduled automation | None known. Use a bounded poll loop, or run `gemini -p` from CI. |

## Models

Set the session model with `--model` or `model.name` in `settings.json` (**unverified**). A subagent file can set its own `model` in frontmatter (**unverified**). When per-spawn model choice isn't available, every role runs on the session model, which is what `auto` does.

Skills read `~/.pstack/models.md` on demand. To preload it, mirror it into `~/.gemini/GEMINI.md`.

## Paths

| What | Where |
| --- | --- |
| Always-applied instructions | `GEMINI.md` in the project and its parents, and `~/.gemini/GEMINI.md`. `context.fileName` in `settings.json` can add `AGENTS.md` (**unverified**). |
| Config | `~/.gemini/settings.json`, `.gemini/settings.json` |
| Session transcripts | Saved chats under `~/.gemini/tmp/<project-hash>/` (**unverified**). Read only this project's folder. |
