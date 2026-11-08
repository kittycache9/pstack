---
name: reflect
description: Spawn three parallel review subagents over the active transcript, surface learnings, and route each to a concrete edit on an existing skill. Use when the user says reflect.
disable-model-invocation: true
---

# Reflect

Mine the current conversation for durable learnings, then route them into skill edits.

Invoke when the user says "reflect" or "/reflect". Skip when the conversation is trivial, off-topic, or already covered by an existing skill the parent followed correctly. One-offs are not learnings.

## 1. Locate the active transcript

Before fanning out, locate the current session transcript the way your harness documents it. Read only this workspace's transcript. Searching other workspaces reads private chats from unrelated projects. When several candidates exist, take the one whose first user message contains the conversation's opening user prompt. If none matches, pass a tight digest of the session instead.

If the harness exposes no transcript, ask the user for an export or skip the transcript read with a one-line explanation and pass the digest. Never read another workspace's transcripts.

## 2. Spawn three reviewers in parallel

Spawn three general-purpose subagents at once, each with normal tool access, including MCP. Reviewers need MCP access to look up the tickets, chat threads, and observability traces the transcript references, so do not use a read-only mode that strips MCP access.

Each reviewer and the synthesizer name a model role. Read `~/.pstack/models.md` when it exists. Use the role's line as the subagent model. Leave the model unset when the value is `auto` or `inherit-parent`, so the subagent runs on the session model. If the file or the role's line is missing, use the session model. If the harness rejects the value, use the session model and say so. When the harness exposes reasoning effort or a speed tier, size it to the job, per the Subagents section of the **poteto-mode** skill.

| Lens | Model role | Prompt template |
|---|---|---|
| Judgment | `reflect judgment, divergent, synthesizer` | `references/judgment-reviewer.md` |
| Tooling | `reflect tooling` | `references/tooling-reviewer.md` |
| Divergent | `reflect judgment, divergent, synthesizer` | `references/divergent-reviewer.md` |

Pass each template verbatim, substituting the transcript path or digest where marked. Reviewers return findings in the subagent's report.

## 3. Synthesize

Spawn one general-purpose subagent with normal tool access, including MCP, on the `reflect judgment, divergent, synthesizer` role. It spot-verifies citations through MCP, so do not use a read-only mode that strips MCP access. Pass `references/synthesizer.md` verbatim, with each reviewer's full output inlined where marked. It returns an Accepted / Rejected / Backlog list.

## 4. Structural enforcement check

Move any Accepted item that a lint rule, script, metadata flag, or runtime check would enforce more reliably to Backlog. See the **encode-lessons-in-structure** principle skill.

## 5. Apply

Present the synthesizer's full Accepted / Rejected / Backlog output and wait for explicit approval before applying any Accepted edit. The user picks the subset and may redirect routings. Skill changes affect every future agent in the org. Do not auto-apply.

File each Backlog item to your team's devex or backlog tracker without waiting. Only the Accepted list waits for approval.

Never edit a skill inside an installed plugin's cache. Before approval, show its destination, `<skill>/` in the project or user skills directory your harness uses. After approval, copy the skill folder there and edit the copy. Never open a PR or push.

Skill authoring below means the harness's skill-authoring tool when it has one; otherwise the **create-skill** skill if installed; otherwise write the `SKILL.md` directly.

Follow each approved row's Routing exactly:

- Trivial existing-skill edit (a one-line bullet, a tightened sentence, a stale fact corrected): the parent does it directly.
- Substantive existing-skill edit (a new section, a new pattern table, more than ~10 lines): hand to skill authoring and run its draft / test / iterate loop when it has one.
- `tune description: <skill path>` (the skill exists but didn't trigger when it should have): hand to skill authoring and run its description-optimization loop when it has one.
- `new skill: <kebab-name>`: hand creation to skill authoring. Do not invent the shape ad hoc.

If your environment ships a SKILL.md validator, run it on every touched skill before declaring done.

## 6. Summarize for the user

Short list, no preamble:

- Edits applied: `<skill path>`. What changed, one line each.
- New skills created: `<skill path>`. One line each (rare).
- Backlog filed to the devex tracker: `<issue title>` (`<tags>`). One line each.
- Dropped: one line per rejected finding + reason from the synthesizer.
