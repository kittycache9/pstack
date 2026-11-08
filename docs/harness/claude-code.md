# pstack in Claude Code

## Install

```text
/plugin marketplace add cursor/plugins
/plugin install pstack@cursor-plugins
```

Claude Code namespaces plugin skills and agents, so `/poteto-mode` is `/pstack:poteto-mode` and the agent profiles are `pstack:poteto-agent` and `pstack:comment-sicko`. When a skill tells the user to type `/name`, write `/pstack:name`.

Claude Code leaves auto-update off for third-party marketplaces. To turn it on, run `/plugin`, open the **marketplaces** tab, select `cursor-plugins`, and choose **enable auto-update**. To update once by hand, run `claude plugin update pstack@cursor-plugins`.

The marketplace serves a generated copy of pstack, including the README and the guide, from [`harness/pstack/`](https://github.com/cursor/plugins/tree/main/harness/pstack) at the repository root. After you change pstack, run `node scripts/generate-pstack-harness-trees.mjs` from the repository root and commit the result. CI fails while the generated copy is out of date.

To install by hand instead, copy or symlink each folder under `<pstack>/skills/` into `.claude/skills/` or `~/.claude/skills/`, and each file under `<pstack>/agents/` into `.claude/agents/` or `~/.claude/agents/`. Hand-installed skills and agents keep their plain names.

## Terms

| Neutral term | Claude Code |
| --- | --- |
| subagent tool | `Agent` |
| a general-purpose subagent | `subagent_type: "general-purpose"` |
| the `poteto-agent` profile, Comment Sicko | `subagent_type: "pstack:poteto-agent"`, `subagent_type: "pstack:comment-sicko"` (no prefix when hand-installed) |
| a read-only subagent | `subagent_type: "Explore"`. It keeps its shell, so the brief still says "do not edit files", and you check `git status` afterward. |
| a normal subagent with tool access, including MCP | `general-purpose`. It keeps the session's MCP servers. |
| in the background | The default. Subagents notify the parent when they finish. |
| a remote worker or isolated worker | `isolation: "worktree"`. Name the branch or commit the worker checks out. |
| resume a subagent | Spawn a new agent and paste the earlier result into its brief. |
| structured question tool | `AskUserQuestion`. It is absent in `-p` mode, so ask in plain text there. |
| todo list | `TodoWrite` when the session offers it |
| file search | `Grep` and `Glob`, or `rg` in the shell when they are absent |
| run another pstack skill | Read `<pstack>/skills/<name>/SKILL.md` and follow it. The `Skill` tool refuses most pstack skills. |
| a persistent mode | None built in. Name the skill again when it fades. |
| recurring-run feature | `/loop`, in versions that ship it. Otherwise a watcher subagent or a bounded poll loop. |
| scheduled or triggered automation | Scheduled cloud routines, in versions that ship them. **Unverified** as a backend for the benny pack. |
| the repository's automated review bot | None built in. A bot's comments on a GitHub PR are ordinary review threads, so triage them with the forge command the step gives. |
| a built-in PR tool | None. Use the forge command the step gives, such as `gh pr create`. That is not a skip. |

## Models

The `Agent` tool's `model` field takes only the aliases its schema lists, such as `opus`, `sonnet`, and `haiku`, and a separate `effort` field. Write a role value as `<model> <effort>`, such as `opus high`, and pass the two fields separately. A value the schema rejects, including a non-Claude model, means: omit `model` so the subagent runs on the session model, and say so. `/setup-pstack` detects models from the `model` and `effort` values the schema lists.

Skills read `~/.pstack/models.md` on demand. To preload it, mirror it into `~/.claude/rules/pstack-models.md` with no frontmatter. Claude Code loads every `.md` file in `~/.claude/rules/` into each new session. Never put the role lines in `CLAUDE.md` or `AGENTS.md`. Earlier pstack versions wrote that rule themselves; move its role lines into `~/.pstack/models.md` and keep the rule only as a mirror.

## Paths

| What | Where |
| --- | --- |
| Skills | `.claude/skills/`, `~/.claude/skills/`, and installed plugins |
| Agent profiles | `.claude/agents/`, `~/.claude/agents/` |
| Plugins | `~/.claude/plugins/`. The manifest is `.claude-plugin/plugin.json`. |
| Always-applied rules | `CLAUDE.md`, or `AGENTS.md` when no `CLAUDE.md` exists, and `~/.claude/rules/*.md`. Claude Code never reads `.mdc` files. |
| pstack docs | `<pstack>/<path>` when the file exists. The plugin ships the skills, `docs/guide/`, and `README.md`. Read the local copy instead of fetching the GitHub URL, and give the user the URL when they need a link. |
| Session transcripts | `~/.claude/projects/<slug>/<session>.jsonl`. The slug is the workspace path with each `/` and `.` changed to `-`, so `/Users/you/proj` is `-Users-you-proj`. The newest `.jsonl` directly in that folder is the current session. Its `<session>/subagents/` folder holds subagent transcripts. Read only this workspace's folder. |
