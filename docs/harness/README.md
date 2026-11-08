# Harness adapters

pstack skills are written for "the harness": whatever agent runtime loads them. They name no product, tool, path, or model slug. This folder is the only place that does. Each adapter maps the neutral terms the skills use to one harness's concrete tools, paths, and settings.

| Harness | Adapter |
| --- | --- |
| Cursor | [cursor.md](./cursor.md) |
| Claude Code | [claude-code.md](./claude-code.md) |
| Codex | [codex.md](./codex.md) |
| opencode | [opencode.md](./opencode.md) |
| Gemini CLI | [gemini-cli.md](./gemini-cli.md) |
| GitHub Copilot and any other agent | [generic.md](./generic.md) |

Harnesses change fast. Each adapter marks a claim **unverified** when it comes from memory or third-party notes rather than the harness's own docs. Check your version's docs before you rely on one, and send a PR when an adapter is wrong.

## What pstack needs

Every skill assumes three things:

- **Skill discovery.** The harness finds a folder that holds a `SKILL.md` with `name` and `description` frontmatter, and loads it when asked or when the description matches. Harnesses that ignore `disable-model-invocation` load those skills only when you name them.
- **A shell and file read/write.** Skills run real commands and read real files. They name the capability, such as "file search" or "shell", not a tool.
- **Subagents.** Most playbooks delegate. A subagent is a fresh agent with its own context, a brief, and a report back. With no subagent support, run each delegate's brief inline, one at a time, and say so.

`<pstack>` in the adapters means the folder that holds `skills/`, `agents/`, and `docs/`. Some skills link to files outside `skills/`, so keep that folder whole and install from it.

## Optional capabilities

A skill that reaches for one of these says what it does without it:

| Capability | Neutral term in skills | Fallback |
| --- | --- | --- |
| Structured multiple-choice questions | structured question tool | Ask in plain text. |
| A todo or plan tool | todo list | Keep the list in the reply and update it each phase. |
| Named subagent profiles | the `poteto-agent` profile, Comment Sicko | Paste `agents/<name>.md` into the subagent brief. |
| Read-only subagents | a read-only subagent | Tell the subagent not to edit files, then check `git status`. |
| Background subagents | in the background when the harness supports it | Run in the foreground. |
| Remote sandboxes or cloud workers | remote worker, isolated worker | A fresh worktree on this machine. |
| Per-subagent model choice | the role's configured model | The session model. |
| Reasoning effort or speed tiers | size it to the job | Leave it unset. The budget only affects model choice. |
| Session transcripts on disk | the current session transcript | Ask for an export, or skip the step. |
| A persistent mode or always-on skill | persistent mode | Name the skill again when it fades. |
| Recurring or scheduled runs | recurring-run or scheduled-task feature | A watcher subagent or a bounded poll loop. |
| Event-driven automations | scheduled or triggered automation | Run the stages by hand or from CI. |
| An automated PR review bot | the repository's automated review bot | Skip the steps that start or wait for it. Its comments are ordinary review threads. |

When a step needs a capability the harness lacks, the skill writes one line, `Skipped: <step>. <reason>.`, and runs the next step. It does not stop, ask, or retry.

## The model config

[`/setup-pstack`](../../skills/setup-pstack/SKILL.md) writes `~/.pstack/models.md`. pstack owns that file, and skills read it on demand when they spawn a subagent, so no harness rule or restart is needed. Each line maps a role to a model value the harness accepts, optionally followed by an effort or speed token. `auto` and `inherit-parent` mean the session model. A missing file or line also means the session model.

```markdown
# pstack model configuration. One line per role. Delete a line to use the session model.
# `auto` or `inherit-parent` means the session model.
# A panel value is a comma-separated list, one subagent per entry.
# budget: large (xhigh)
feature, refactoring: auto
judgment and prose: auto
interrogate reviewers: auto
```

The full role list lives in `setup-pstack`. If you want the roles preloaded into every session, mirror the file into your harness's always-loaded rules file. Each adapter names that file. Keep `~/.pstack/models.md` as the source, and re-mirror after each `/setup-pstack` run.

Older pstack versions wrote the config as a harness rule, such as `.cursor/rules/pstack-models.mdc` or `~/.claude/rules/pstack-models.md`. Move the role lines from that file into `~/.pstack/models.md`, then delete the old rule so the two can't disagree.

## The shared skills directory

Several harnesses read a cross-agent skills directory, `.agents/skills/` in a project and `~/.agents/skills/` for the user. One copy there serves all of them:

| Harness | Reads `.agents/skills/` | Native directory |
| --- | --- | --- |
| Cursor | yes | `.cursor/skills/` |
| Claude Code | not documented | `.claude/skills/` |
| Codex | yes, as its only repo and user location | none |
| opencode | yes | `.opencode/skills/` |
| Gemini CLI | yes, as an alias | `.gemini/skills/` |
| GitHub Copilot | yes | `.github/skills/` |

Cursor, opencode, and Copilot also read `.claude/skills/`, so a copy there reaches Claude Code too.
