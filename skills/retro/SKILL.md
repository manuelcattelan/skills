---
name: retro
description: "Reviews past agent sessions in the current project, the current one by default or the ones the user describes, for improvements to the agent's working environment: context and tool costs, the user's skills, requests repeated often enough to deserve a skill, steering files such as CLAUDE.md and AGENTS.md, automated checks, and navigation. Reports every finding with its evidence, ranked by how often it recurs and what it wastes, and changes nothing."
disable-model-invocation: true
---

Review agent sessions for improvements to the agent's **environment**: everything around the model that shapes how a session goes, such as steering files, skills, checks, and tools. A finding is never "the agent should have tried harder": it is what in the environment let a session go worse than it should have, so that changing it makes the next session go better.

Find and report, never fix: write and edit nothing, not even a notes file, so that each fix is taken into a dedicated session that has the context to make it well.

1. Resolve the sessions following [Sessions](#sessions).
2. Review them following [Review](#review).
3. Present the findings following [Report](#report).

## Sessions

Review the current project only, its worktrees included. Before anything else, find out where the coding agent you are running in, such as Claude Code or Codex, saves on disk the transcripts of this project's sessions, and the history of the prompts the user typed, if it saves one. Check its documentation or explore its configuration directory rather than assuming a location: each agent saves them in its own place, which can change between versions.

- With no argument, review the current session, up to the invocation of this skill. When its transcript can't be found, review the conversation itself.
- With an argument, read it as free text describing which sessions to review, such as "the last 3 days", "this week", "the last 5 sessions", or one or more session IDs, and resolve it against the sessions' timestamps.

When nothing matches, say so and stop.

## Review

Run one general-purpose subagent per session, or per batch of small sessions, all started in the same turn, in the foreground. Each of those subagents sees a single session, so none of them can notice a request the user repeats across sessions. When more than one session is reviewed, also run one subagent on the prompts the user typed in all of them, looking for **Repeated requests**.

Subagents don't see this conversation, so give each one a self-contained brief: where to find its transcripts or prompts, the rule to change nothing, the [Categories](#categories) section below, and how to report findings, as described below. List in it the steering files to compare the sessions against, in the project and in the user's global configuration: instruction files such as `CLAUDE.md` and `AGENTS.md`, glossaries, rules, settings, and skills, the installed plugins' included.

Leave them free to explore the transcripts however they see fit. Point out only that transcripts are large, so they query them rather than read them whole, and that they usually record the token usage of every model call, the tool calls with their results, compactions, and the user's own messages, corrections and interruptions included.

Each subagent reports every **finding** like the one below, where each label says what its part includes:

```md
- **Category**, one of the categories below: Steering files
- **Evidence**, the session ID and the moments in it, such as a tool call, a user correction, or a token count: session `3f2a9c1e`, the `npm install` call, and the user's correction right after it: "we use pnpm, not npm"
- **What went wrong, and what it wasted**, in tokens, time, turns, or the user's corrections: the agent installed with npm in a pnpm project, because `CLAUDE.md` doesn't name the package manager. It wasted a 40-second install, a stray `package-lock.json`, and a correction from the user.
- **Direction** for the fix, naming the file, skill, or tool it belongs in: name pnpm as the package manager in `CLAUDE.md`, or block `npm` with a hook that points to pnpm.
```

### Categories

A finding starts from friction a session shows: a correction or interruption from the user, a failed or repeated command, a slow or oversized call, a compaction. Read the project's files only to explain such a moment or to locate its fix, never to hunt for problems the sessions didn't run into: judging the code they produced is the job of a code review, not of a retrospective.

- **Context economy**: context spent on what didn't need it, such as whole large files read where a search would do, verbose tool output, the same content read twice, MCP tool definitions or steering files loaded and never used, exploration a subagent should have held, or a session pushed into compaction. The fix keeps the content out of the main context.
- **Tool cost**: tool calls that cost far more than they returned, such as retry loops, commands failing the same way repeatedly, redundant calls, slow or token-heavy custom tools, or permission prompts the user approves every time. The fix is a cheaper call, a better tool, or a standing permission.
- **Skill behavior**: a skill that went wrong: it didn't trigger when it should have, triggered when it shouldn't have, its instructions were skipped or misread, or the user had to correct its output. Point the fix at the instruction responsible, in the skill's source rather than in an installed copy: a local repository when the user maintains one, otherwise upstream.
- **Repeated requests**: requests the user types from scratch again and again, with the same steps or the same corrections each time. The fix is a new skill, or a new step in an existing one, that standardizes the request.
- **Steering files**: `CLAUDE.md`, `AGENTS.md`, rules, and glossaries. Report an instruction whose absence led to a mistake or a correction, and an instruction that misled the agent or that it ignored. These files load into every session, so the fix keeps them short: a pointer to a document rather than its content, and a check rather than prose wherever one could enforce the rule.
- **Automated checks**: a mistake the agent made in a session, surfaced by an error or a correction, that a linter, type checker, test, hook, or CI job could have caught first. A mechanical rule, such as a banned API, an import shape, or a file location, gets a deterministic check rather than an instruction.
- **Navigation and information access**: the agent searched long for a file or fact, or lacked information it needed, such as logs, a service's state, or documentation. The fix is a pointer in a file the agent already reads, or access to the information.

## Report

Before reporting, check that each finding's evidence holds, and drop the ones whose evidence doesn't, that rest on general advice, or that no moment of friction in a session led to. Merge the findings that share a cause, within a session or across sessions, keeping the evidence of each.

Rank each finding `High`, `Medium`, or `Low` by how often it recurs, across moments and sessions, times what each occurrence wastes. A modest waste in every session outranks a single expensive one.

Open with one line naming the sessions reviewed: how many, and the time span they cover. Leave out how the review went and what it didn't find. Then present the findings under `## High`, `## Medium`, and `## Low` headings, leaving out empty ones, numbered across the whole report:

```md
## High

### 1. <what goes wrong>
**<category>** · <session IDs and moments>

<the evidence, what went wrong, and what it wastes>
**Direction**: <the fix, at a high level, and where it belongs>
```


Never pad the report: a category that finds nothing reports nothing, and a short or empty report is a good result. End with the report, asking nothing.
