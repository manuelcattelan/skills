---
name: upkeep
description: "Reviews the whole codebase, or one directory, for any sign of decay in the codebase: duplicated, tangled, or dead code, drift from the project's conventions, test anti-patterns, documentation the code no longer matches, and code or documentation that doesn't follow the glossary, deprecated entries included. Ranks the findings, settles the ones the user picks with them either with interview or scout, then hands the fixes to specalize."
disable-model-invocation: true
---

Review the codebase for **decay**: the problems that pile up when agents add code faster than anyone reads it as a whole, until the project turns into a ball of mud. Every effort is audited on its own, but nothing checks how the efforts add up: a pattern copied across the project, a layer nobody needed, a `README.md` describing outdated behavior.

The perimeter to review is either the whole codebase, or only the directory passed as an argument. With a directory, report only problems that lie in it, including documentation and glossary entries about its code: read the rest of the repository only as evidence, such as the other copy of a duplication.

Find and decide, never fix: change no code, documentation, or glossary, so that every fix lands through a spec, reviewed and audited like any other change. Take the `GLOSSARY.md` files as the authority on the project's vocabulary: whether they are right is `/define review`'s job, which a full review runs first.

1. Review the perimeter.
2. Map the project following [Map](#map).
3. Run the checks following [Checks](#checks).
4. Present the findings following [Report](#report), and let the user pick the ones to fix.
5. Settle the picked findings following [Picks](#picks).

## Map

The checks must work in any project, whatever its architecture, so each one describes what it looks for and why it matters, and a map gives it its meaning inside a project.

Before the checks, run one **exploration** subagent, in the foreground, that saves the map as a markdown note in the system's temporary directory, never inside the repository, recording anything that would give checks a more concrete definition that fits with the underlying project.

This includes the project's areas and dependency rules between them, documented conventions, `README.md`, `CLAUDE.md`, and `AGENTS.md` files, `GLOSSARY.md` files, the testing architecture.

## Checks

Run each family of checks below in its own general-purpose subagent, all started in the same turn, in the foreground, so that no family's reasoning shapes another's.

Subagents don't see this conversation, so give each one a self-contained brief with the path to the map, the perimeter and the rule for reviewing a directory, and its family's section below. Tell it to report every **finding** with:
- the check it fails.
- every `file:line` it concerns, with the evidence it rests on quoted verbatim.
- what is wrong, and a brief direction for the fix.
- what leaving it would cost, following [Priority](#priority).

### Structure

- **Duplication**: the same logic, or parallel implementations of one behavior, in more than one place. The copies drift apart, and agents copy whichever one they find first. Code that only looks alike, but serves different concepts and would change for different reasons, is not duplicated.
- **Coupling**: parts of the code that reach into each other's internals instead of going through what each one exposes, or that keep changing together for unrelated reasons.
- **Dependency direction**: import cycles, and dependencies that run against the rules the map records.
- **Needless indirection**: layers that only pass calls through, wrappers that add nothing, and abstractions with a single implementation and no stated reason to exist.
- **Scattered concepts**: the logic of one concept spread across unrelated areas, or code living in an area other than the one its concept belongs to, so that changing the concept means hunting for it.
- **Dead code**: files, exports, functions, flags, configuration, and dependencies that nothing uses. Check the entry points, dynamic loading, and the project's public interface before calling something unused.
- **Overgrown units**: files, classes, or functions that have grown to do several unrelated jobs.

### Conventions

Find out how this project expects its code to be written, from wherever it documents it, and report every place where the code departs from that, quoting the rule it breaks. The documentation is always the authority: a departure from a documented rule is a finding even when most of the code does the same.

Where the documentation is silent, the existing code sets the convention by being consistent. Report the places that depart from such a pattern, citing the code that establishes it too.

### Tests

Report tests that fall into these anti-patterns, and leave missing tests alone:
- **Implementation-coupled tests**: a test that mocks the project's own modules, calls private functions, or checks its result through a side channel, such as querying the database instead of the interface. It breaks when the code is refactored even though the behavior didn't change.
- **Tautological tests**: a test whose expected value is computed the way the code computes it, so that it passes by construction. Take expected values from an independent source instead.

### Documentation

Check the `README.md`, `CLAUDE.md`, and `AGENTS.md` files, and the code comments and docstrings, against the code:
- **Contradicted**: they state behavior, commands, configuration, or setup steps that the code contradicts.
- **Dangling**: they reference files, scripts, flags, environment variables, links, or anything else that no longer exists.
- **Gaps**: one of them lists something the code has, such as features, commands, endpoints, or environment variables, and misses an item the code now has. Report a gap only where a document already lists that ground: a feature nothing documents is not a finding.

See [Glossary](#glossary) to learn how to deal with `GLOSSARY.md` files.

### Glossary

Check the code and the documentation the previous section covers against the `GLOSSARY.md` files, which are the authority. Look for each concept by what its definition describes, not only by its term, and match words by meaning:
- **Deprecated terms**: any remaining use of a term from a `## Deprecated` group. When its `Deprecated` line names a replacement, the fix is renaming it to that term, migrating any stored data, interface fields, or events it names; when it names none, the uses are leftovers of a retired concept to remove.
- **Off-glossary names**: a concept that has an entry, called by any word other than its term, including words on the entry's `Avoid` line.
- **Contradicted entries**: code or documentation that contradicts an entry's definition, or treats as one two concepts its `Distinct from` line tells apart.
- **Unused deprecated entries**: an entry under `## Deprecated` whose term nothing in the code or the documentation uses anymore. The fix is removing the entry and, when its `Deprecated` line names a replacement, adding the term to that entry's `Avoid` line unless it is already there.
- **Stale deprecated entries**: an entry under `## Deprecated` whose `Deprecated` line names a replacement that has since been renamed, moved, or deprecated, or whose links point to entries that no longer exist. The fix is pointing it at the current entry, following the chain of replacements, and unlinking what has no entry.

## Report

Before reporting, check that each finding's evidence holds, and drop the ones whose evidence doesn't. Then read the closed issues labelled `upkeep:dismissed` and drop every finding one of them dismissed, unless the evidence shows that its reason no longer holds: then keep it, citing the dismissal.

### Priority

Rank each finding `High`, `Medium`, or `Low` by what leaving it would cost:
- how far it will spread, since agents copy the patterns they find.
- how much of the code it reaches, to evaluate the blast radius more precisely.
- whether it misleads agents: a stale `CLAUDE.md`, `AGENTS.md`, or `README.md` is read and acted on in every session.

Never pad the report: a check that finds nothing reports nothing, and a short or empty report is a good result.

### Format

Present the findings under `## High`, `## Medium`, and `## Low` headings, leaving out empty ones, numbered across the whole report:

```md
## High

### 1. <what is wrong, in the glossary's terms>
**<check>** · `<file:line>`, `<file:line>`
> <evidence quoted verbatim>

<what is wrong, and what leaving it would cost>
**Fix**: <brief direction>
```

Then ask the user which findings to fix, by number or by heading, and whether any of the others should be dismissed for good.

## Picks

When the user dismisses a finding for a lasting reason, such as a duplication kept on purpose, record it as a closed issue labelled `upkeep:dismissed` on the project's issue tracker, creating the label when it is missing, so that later reviews drop it. Do this both when they pick and during the interview. A finding they just don't pick is not dismissed: later reviews raise it again.

Settle the picked findings in one of two ways, both ending in a spec that `/specalize` writes from the decisions:
- **Interview**: when the picked findings fit one interview session, settle them in this one following [Interview](#interview). When the user agreed to any fix, finish by telling them to run `/specalize` in this same session.
- **Scout**: when they need more decisions than one interview session can hold before its context window fills up, stop and tell the user to run `/scout` on them in this same session. Its trails settle the findings across sessions, and `/specalize <map>` writes the spec once none is left open.

### Interview

Run the session with the `skills:interview` skill, putting each picked finding to the user as one question whose recommendation states the fix. When the fix is text, such as a glossary entry, a `README.md` passage, or a comment, show it in a fenced markdown block exactly as it would read.

The evidence stays in the conversation: the fix never cites it. When the user sides with the code over a glossary entry, the decision is the entry's change instead. Every fix, glossary changes included, lands through the spec. Once the user confirms the shared understanding, write nothing but the dismissals.
