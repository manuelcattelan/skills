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

- **`/define`**: fixes the project's vocabulary. It interviews you about the project's concepts, settles one term and one precise definition for each, and records them in `GLOSSARY.md` files that the other skills use when they write specs and tickets. Run it before the main workflow, and again whenever a concept is named with a vague or overloaded word. `/specalize` also runs it on just the concepts each spec adds, changes, renames, or retires, so the glossary changes land with the code they describe. Run `/define review` from time to time, or `/define review <glossary or directory>` for a single area, to check the glossaries against the code: it raises entries that are stale, missing, overlapping, or vague, and settles each with you. It changes nothing itself: run `/specalize` in the same session to turn the agreed changes into a spec. It only decides whether the glossary is right; code and docs that don't follow it are left to `/upkeep`'s glossary checks, run once the glossary changes land.

### Main workflow

#### 1. Design

- **`/interview`**: for an effort one session can hold. It interviews you about a plan, design, or idea, one round of questions at a time, each with a recommended answer, until you share an understanding of it. Its decisions are what the rest of the workflow builds from. It is also useful on its own, whenever you want your thinking stress-tested.
- **`/scout`**: for an effort too large for one session, such as a large refactor or a feature you only know at a high level. It draws a **map** on the project's issue tracker: one issue whose sub-issues are **trails**, each a domain or angle of the effort. Each later `/scout` session follows one trail, interviewing you about it, while research trails run on their own in the background. The map grows as answers make new questions clear, until nothing is left to decide.

#### 2. Requirements

- **`/specalize`**: turns the decisions into a spec published to the project's issue tracker, written so that later agent sessions can build from it without seeing the conversation. Run it at the end of an `/interview`, `/define review`, or `/upkeep` session, or as `/specalize <map>` once a map has no open trails left. Before drafting, it settles with you, through `/define`, the glossary changes the decisions require, and records them in the spec. Apart from those, it records only what was decided: it asks nothing and decides nothing.
- **`/ticketize`**: breaks a spec into tickets that fresh agent sessions can implement without seeing the conversation. Each ticket is a tracer bullet, a narrow but complete path through every layer the change touches, and lists the tickets blocking it. When the spec changes the glossary, a dedicated ticket applies those changes first, blocking the tickets that use the new terms. Once you approve the split, `/ticketize` publishes them as sub-issues of the spec.

#### 3. Implementation

- **`/implement`**: implements a spec and all its tickets in one autonomous run. Subagents build each ticket test-first in its own worktree, as soon as its blockers land on a single integration branch. Once every ticket has landed, `/implement` runs `/audit`, has the findings fixed, and closes the spec and its tickets through one pull request.
- **`/audit`**: audits a branch against its spec and tickets in a fresh context, along three axes: faithfulness to the spec, its glossary changes included, conformity with the project's conventions, and refactoring opportunities. `/implement` runs it once every ticket has landed, before the pull request goes up for review, but you can also run it on any branch that implements a spec.

### Maintenance

- **`/upkeep`**: reviews the whole codebase, or the directory you pass it, for any sign of decay in the codebase: duplicated, tangled, or dead code, drift from the project's conventions, tests coupled to the implementation, documentation the code no longer matches, and code or documentation that doesn't follow the glossary, deprecated terms and their entries included. It presents the findings ranked by what leaving them would cost, lets you pick the ones to fix, and settles each with you either through `/interview` or, when they are too many for one session, through `/scout`, then hands them to `/specalize`: in the same session after an interview, or as `/specalize <map>` once the map has no open trails left. It changes nothing itself. A full review runs `/define review` first and lands its changes, since `/upkeep` takes the glossary as its authority.

### General-purpose

- **`/research`**: checks a planned approach against official, high-trust sources before any code is written. It doesn't belong to a single workflow: the agent runs it on its own whenever it is about to write code or propose a design that uses a library, framework, API, or tool, but you can also run it directly for any research question.
