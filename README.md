# skills
Software engineering skills for agentic workflows

## Install

The skills ship as a Claude Code plugin. Install it from this repository, which adds the `manuelcattelan` marketplace on the way:

```sh
claude plugin install skills --marketplace manuelcattelan/skills
```

Then turn on auto-update: run `/plugin`, open **Marketplaces**, select `manuelcattelan`, and select **Enable auto-update**.

To develop and test skills, install from a local clone instead. Edits then load at the next session start:

```sh
claude plugin install skills --marketplace /path/to/skills
```

## Skills

### Preliminary step

- **`/define`**: fixes the project's vocabulary. It interviews you about the project's concepts, settles one term and one precise definition for each, and records them in `GLOSSARY.md` files that the other skills use when they write specs and tickets. Run it before the main workflow, and again whenever a concept is named with a vague or overloaded word.

### Main workflow

- **`/interview`**: interviews you about a plan, design, or idea, one round of questions at a time, each with a recommended answer, until you share an understanding of it. Its decisions are what the rest of the workflow builds from. It is also useful on its own, whenever you want your thinking stress-tested.
- **`/specalize`**: turns the decisions made in the session into a spec published to the project's issue tracker, written so that later agent sessions can build from it without seeing the conversation. It records only what was decided: it asks nothing and decides nothing.
- **`/ticketize`**: breaks a spec into tickets that fresh agent sessions can implement without seeing the conversation. Each ticket is a tracer bullet, a narrow but complete path through every layer the change touches, and lists the tickets blocking it. Once you approve the split, `/ticketize` publishes them as sub-issues of the spec.
- **`/implement`**: implements a spec and all its tickets in one autonomous run. Subagents build each ticket test-first in its own worktree, as soon as its blockers land on a single integration branch. Once every ticket has landed, `/implement` runs `/audit`, has the findings fixed, and closes the spec and its tickets through one pull request.
- **`/audit`**: audits a branch against its spec and tickets in a fresh context, along three axes: faithfulness to the spec, conformity with the project's conventions, and refactoring opportunities. `/implement` runs it once every ticket has landed, before the pull request goes up for review, but you can also run it on any branch that implements a spec.

### General-purpose

- **`/research`**: checks a planned approach against official, high-trust sources before any code is written. It doesn't belong to a single workflow: the agent runs it on its own whenever it is about to write code or propose a design that uses a library, framework, API, or tool, but you can also run it directly for any research question.
