# pstack in Cursor

pstack started as a Cursor plugin, so most neutral terms in the skills map directly onto a Cursor feature.

## Install

In a Cursor chat, run `/add-plugin pstack`. The plugin manifest is [`.cursor-plugin/plugin.json`](../../.cursor-plugin/plugin.json), and it registers `skills/` and `agents/`. Plugins install under `~/.cursor/plugins/`.

To install by hand instead, copy or symlink each folder under `<pstack>/skills/` into one of the directories Cursor scans:

- Project: `.agents/skills/` or `.cursor/skills/`
- User: `~/.agents/skills/` or `~/.cursor/skills/`
- Compatibility: Cursor also reads `.claude/skills/`, `.codex/skills/`, and their `~/` forms.

Type `/` and pick a skill, or type its name, to attach it to one message.

## Terms

| Neutral term | Cursor |
| --- | --- |
| subagent tool | `Task` |
| a general-purpose subagent | `subagent_type: generalPurpose` |
| the `poteto-agent` profile, Comment Sicko | `subagent_type: "poteto-agent"`, `subagent_type: "comment-sicko"` |
| a read-only subagent | `readonly: true` |
| a normal subagent with tool access, including MCP | `readonly: false` |
| in the background | `run_in_background: true` |
| a remote worker or isolated worker | `environment: "cloud"`, a cloud subagent with its own VM and branch. Type `/in-cloud` before a task, or ask the parent chat to hand work to cloud subagents. |
| have the worker check out `<branch>` first | `cloud_base_branch: <branch>` |
| structured question tool | `AskQuestion` |
| todo list | `TodoWrite` |
| a persistent mode | A [Custom Mode](https://cursor.com/docs/skills#using-a-skill-as-a-custom-mode). Pick the skill from the `/` menu and press Option+Enter (Mac) or Alt+Enter (Windows) instead of Enter. It stays in context every turn until you exit it. Available in the Agents Window and the CLI. |
| recurring-run feature | `/loop`, Cursor's built-in wake mechanism. The Autonomous run playbook uses it to re-check the finish condition on events or a heartbeat. |
| scheduled or triggered automation | Cursor Automations. The [benny pack](../../automations/benny/README.md) targets them. |
| the repository's automated review bot | Bugbot |
| a standing coordinator session | A [Cursor Project](https://cursor.com/blog/projects): one coordinator with a persistent thread, whose subagents run in the cloud by default. Drag related chats into the Project to share their context. |
| the harness's skill-authoring tool | the built-in `/create-skill` |
| a built-in PR watcher | the built-in `/autopilot`. Inside `poteto-mode`, PR-status requests go to the Babysit playbook instead. |
| the repo's slop-strip skill, control skills | `/deslop`, `control-ui`, and `control-cli` ship in the `cursor-team-kit` plugin. Install it alongside pstack if you want them. |

## Models

The subagent `model` field takes a slug from the Cursor model picker. Slugs can carry an effort suffix, such as `-xhigh`, and a `-fast` speed suffix, as in `grok-4.7-xhigh-fast`. `/setup-pstack` detects the slugs `Task` accepts and writes them as role values. Leaving `model` unset runs the subagent on the chat's model, which is what `auto` and `inherit-parent` do.

To preload the config, mirror `~/.pstack/models.md` into a rule at `.cursor/rules/pstack-models.mdc` with `alwaysApply: true` frontmatter. Earlier pstack versions wrote `~/.cursor/rules/pstack-models.mdc` themselves; move its role lines into `~/.pstack/models.md`.

## Paths

| What | Where |
| --- | --- |
| Always-applied rules | `.cursor/rules/*.mdc` with `alwaysApply: true`, or `AGENTS.md` |
| Session transcripts | `~/.cursor/projects/<slug>/agent-transcripts/`. Read only the current workspace's folder. |
| Installed plugins | `~/.cursor/plugins/` |
