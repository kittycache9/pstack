---
name: setup-pstack
description: Configure which models pstack uses per role and at what reasoning budget. Detects the models your harness exposes and writes the pstack model config at ~/.pstack/models.md. Use for /setup-pstack, "configure pstack models", "pstack budget", or changing pstack's model choices.
---

# Setup pstack

Write `~/.pstack/models.md`, the pstack model config. It records one budget and one model per pstack role. The skills and agents read it on demand, so it needs no always-applied harness rule.

## Steps

### 1. Load current state

If `~/.pstack/models.md` already exists, read it and treat its `# budget` line and its role values as the current choices. Otherwise start from every role set to `auto`. A line whose role is not in the canonical shape in step 7, such as `how critics`, is from a retired role. Drop it.

### 2. Detect available models

Enumerate the model values this session can pass to a subagent. Use the values the subagent tool accepts, the harness's model list, or a command the harness documents for listing models. If you cannot detect any, ask the user to name the models they have access to. Never write a model selector you have not confirmed the harness accepts. Do not assume a naming scheme, family prefix, effort ladder, or speed suffix. The aliases `inherit-parent` and `auto` are always valid even though they are not detected models.

### 3. Ask for a budget

Use the structured question tool when the harness has one, otherwise ask in plain text. Offer these four labels, and name the current budget when the config records one. With no config, say that `large` matches the skill defaults.

- `unlimited — max reasoning`
- `large — xhigh reasoning`
- `medium — high reasoning`
- `small — medium reasoning`

### 4. Apply the budget

Apply the budget to the effort scale the harness exposes. Map the four labels to the closest levels it has, at or below the target: `unlimited` to its highest level, `large` one below, `medium` two below, and `small` three below, or to the nearest exposed level when it has fewer.

On a re-run, keep any role the user set to an explicit model. Adjust the effort or speed token on those values only when the harness accepts the adjusted value. If it rejects one, keep the configured value, then fall back to the session model, and say so.

`auto` and `inherit-parent` never change. If the harness has no effort control, leave every model value unchanged and say the budget only affects model choice.

### 5. Show the roles and confirm

Show every role with its model. Mark any model value the harness did not confirm as needing a choice. Also list each line step 1 dropped. Ask whether to accept as-is or change specific roles, offering the confirmed models plus `inherit-parent` and `auto` as the options. Both aliases mean the role runs on the session model, which is how a user who leaves model choice to the harness stays there. Use the structured question tool when the harness has one, otherwise ask in plain text.

Default every role to `auto` unless the user asks to map roles to distinct models. Offer that mapping when the harness exposes more than one model.

For panel roles (arena runners, architect runners, interrogate reviewers) the value is a comma-separated list, and one subagent runs per entry, alias entries included, so the list length sets the count. `arena cross-judge pool` is also a list, but Arena runs one judge. It tries only values from a model family other than the session model's, in list order, so order it by preference. An `inherit-parent` or `auto` value never judges. `swarm workers` is the default model for every worker unless a race or comparison assigns another model per arm.

### 6. Validate

Every explicit model value written must be one the harness confirmed. `inherit-parent` and `auto` always pass. If a chosen value is not available, stop and ask again.

### 7. Write the config

Write `~/.pstack/models.md`. Overwrite the whole file so re-runs stay idempotent. Keep a `# budget:` line with the chosen label and its target effort when the harness exposes effort, and one line per role using these labels. Canonical shape:

```markdown
# pstack model configuration. One line per role. Delete a line to use the session model.
# `auto` or `inherit-parent` means the session model.
# A panel value is a comma-separated list, one subagent per entry.
# budget: large (xhigh)
feature, refactoring: auto
bug-fix: auto
perf-issue: auto
hillclimb: auto
judgment and prose: auto
hardest tasks: auto
how explorer: auto
how explainer: auto
why investigators: auto
why synthesizer: auto
reflect tooling: auto
reflect judgment, divergent, synthesizer: auto
arena runners: auto
arena cross-judge pool: auto
swarm workers: auto
architect runners: auto
interrogate reviewers: auto
```

If the harness has no effort control, write the budget line without an effort token, such as `# budget: large`.

### 8. Confirm

Tell the user the config was written and that skills read it on demand in future sessions. Re-running this skill updates it.

### 9. Offer a verification skill (optional)

Check whether the project has a way to drive the real app for proof (a `verify-*` skill, or an existing test harness). If not, offer once: "want a project-local verification skill, so agents can drive the app the way a user does and prove changes work? I can generate one with /create-verification-skill." On yes, invoke `/create-verification-skill` (resolves wherever pstack is installed: workspace, user, or plugin). On no, move on without pushing.

### 10. Offer a formal verification skill (optional)

Check whether the project already has a formal verification skill (a project skill that runs a model checker, a proof, or another verifier against the code). If not, offer once: "want a formal verification skill, so agents can prove a key property of your code holds in every case in scope, not only the cases tests reach? I can generate one with /create-formal-verification-skill." On yes, invoke `/create-formal-verification-skill` (resolves wherever pstack is installed: workspace, user, or plugin). On no, move on without pushing.
