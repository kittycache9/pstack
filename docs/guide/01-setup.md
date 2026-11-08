# Set up pstack

In this page you install pstack, pick which models it uses, and run your first task. Setup is one command plus a short conversation.

## Install pstack

In Cursor, run this in a chat:

```text
/add-plugin pstack
```

In Claude Code, run `/plugin marketplace add cursor/plugins`, then `/plugin install pstack@cursor-plugins`. Claude Code prefixes plugin commands, so each command in this guide takes a `pstack:` prefix there, such as `/pstack:setup-pstack`.

In Codex, opencode, Gemini CLI, GitHub Copilot, or any other agent that reads a skills directory, copy or symlink each folder under `skills/` into that directory. `.agents/skills/` in the project, or `~/.agents/skills/` for every project, works for most of them. The [install table](../../README.md#install) lists each harness's directories, and the [harness adapters](../harness/README.md) cover the other differences.

## Pick your models

Run:

```text
/setup-pstack
```

[`/setup-pstack`](../../skills/setup-pstack/SKILL.md) detects the models you have access to, asks for a reasoning budget, shows you each role (code delegates, judgment, the review panels), and asks what you want. Answer the questions. It writes `~/.pstack/models.md`, a small file every pstack skill reads when it spawns a subagent.

The budget sets reasoning effort when your harness exposes it. `large` runs each role at a high effort. `unlimited` lifts each model to the highest tier it offers, and a model that tops out lower stays at its top. `medium` and `small` lower the effort and spend fewer tokens. If your harness has no effort control, the budget only affects which models you pick.

You only override what you care about. A role with no line in the file runs on the session model. To restore the default, delete that role's line. A rerun of `/setup-pstack` keeps any role whose model differs from the default. When a default changes, a file written before the change still pins the old choice, so delete those role lines, or delete the file, then run `/setup-pstack` again.

Every role starts on `auto`. Set a role to `auto` or `inherit-parent` and pstack leaves the subagent's model unset, so the subagent runs on the session model. Both values mean the same thing, and neither is a model name. For a panel role the value is a list, and one subagent runs per entry, so the list length sets the panel size. Setup also configures `swarm workers`, the default model for every `/swarm` worker unless a race names a model for each arm.

## Accept the verification offers, or don't

At the end of setup, `/setup-pstack` looks for a way to prove app behavior in your project, either a `verify-*` skill or an existing test harness. If it finds neither, it offers once to generate one with [`/create-verification-skill`](../../skills/create-verification-skill/SKILL.md).

Say yes and it writes `verify-<app>/` into your project's skills directory, a project-local skill that teaches agents to drive your app the way a user does. It proves the skill works once before handing it over. Say no and setup moves on. You can run `/create-verification-skill` yourself any time. [Verify and ship](./06-verify-and-ship.md#create-a-project-verification-skill) covers it in depth.

If you're new to pstack, say yes. An agent that can check its own work keeps going until the check passes. An agent that can't hands every result back to you to check by hand. Of everything in this guide, the verification skill pays off the most.

Then setup offers once to generate a formal verification skill with [`/create-formal-verification-skill`](../../skills/create-formal-verification-skill/SKILL.md), unless the project already has one. Tests check the cases someone thought to write. A formal verification skill checks one key property of your code, such as "a job never runs twice at once", in every case within a stated scope. Say yes when your code has concurrency, retries, a state machine, or a parser, where a bug is costly and tests rarely reach the case that breaks. Otherwise say no, and run it later if that changes. [Verify and ship](./06-verify-and-ship.md#prove-a-property-with-a-formal-verification-skill) covers it.

Skills read `~/.pstack/models.md` each time they spawn a subagent, so the change applies right away. If you want the roles preloaded into every session, you can mirror the file into your harness's always-loaded rules file. The [harness adapters](../harness/README.md) name that file. Start a new session after you do, so the harness reloads it.

## Keep the cost in check

pstack spends extra tokens on subagents and review panels. That's the price of the rigor. To spend fewer:

- Rerun `/setup-pstack` and pick a smaller reasoning budget or cheaper models. A strong model in the main chat with cheaper, faster models in the code roles is a good split.
- Set a role to `auto` or `inherit-parent` so it runs on the session model.
- Shorten a panel list. Each entry runs one subagent.
- Save `/poteto-mode` for work that needs rigor. A small, obvious edit doesn't.

## Run your first task

Pick something real but small, and describe it the way you'd describe it to a colleague:

```text
/poteto-mode add a --json flag to this command. text output stays byte-identical. verify both.
```

Watch the todo list. Its first items are the matched playbook's steps copied in, the Feature playbook for this prompt. If `/poteto-mode` skips a step, the step stays in the list with `skip: <reason>`, so you can see what it chose not to do.

From here you can type normal follow-ups. A skill you invoke once attaches to one message, and it fades as the session moves on. To keep `/poteto-mode` on for the whole session, use your harness's persistent mode or always-on skill if it has one. It stays in context on every turn until you exit it. The [harness adapters](../harness/README.md) name the keystroke or setting. Without one, name the skill again when its rules start to slip.

Next: [Route work through `/poteto-mode`](./02-poteto-mode.md).
