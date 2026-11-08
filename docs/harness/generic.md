# pstack in any other agent

Use this page for GitHub Copilot and for any harness without its own adapter.

## Install

Copy or symlink each folder under `<pstack>/skills/` into the project or user skills directory your harness reads. Try the shared directory first: `.agents/skills/` in a project, `~/.agents/skills/` for the user. Many harnesses read it.

```bash
git clone https://github.com/cursor/plugins ~/src/cursor-plugins
mkdir -p ~/.agents/skills
for d in ~/src/cursor-plugins/pstack/skills/*/; do ln -s "$d" ~/.agents/skills/; done
```

Symlinks keep pstack current with a `git pull`. Some skills link to `agents/` and `docs/`, so keep the clone where it is.

GitHub Copilot reads project skills from `.github/skills/`, `.claude/skills/`, or `.agents/skills/`, and personal skills from `~/.copilot/skills/` or `~/.agents/skills/`. Skills work in the Copilot app, Copilot CLI, the cloud agent and code review on GitHub.com, and agent mode in VS Code, Visual Studio, and JetBrains IDEs, depending on version. For cloud sessions, personal skills go in `.copilot/skills/` in your dotfiles repository.

If your harness has no skills directory, add the `SKILL.md` you need to its rules or context file, or paste it into the session. Start with `skills/poteto-mode/SKILL.md`; it tells the agent which other files to read.

## Map the terms

For each row in the [optional capabilities table](./README.md#optional-capabilities), find your harness's tool or setting. Where there is none, the fallback in that table applies. Then fill in:

- **Subagent tool.** The tool that spawns a delegate with its own context. With none, run each brief inline and say so.
- **Agent profiles.** If the harness supports named agents, install `agents/poteto-agent.md` and `agents/comment-sicko.md` in its agents directory, adjusting the frontmatter to its format. Otherwise paste the file into the brief.
- **Model selection.** Whether a spawn takes a model value, and in what form. `/setup-pstack` detects this from the tool's fields; with none, every role runs on the session model.
- **Always-loaded rules.** The file the harness reads into every session, such as `AGENTS.md` or `.github/copilot-instructions.md`. Mirror `~/.pstack/models.md` there only if you want the roles preloaded.
- **Transcripts.** Where the harness stores the current session, if it does. Skills that need one ask for an export or skip the step.

If you map a harness fully, send the result as a new adapter in this folder.
