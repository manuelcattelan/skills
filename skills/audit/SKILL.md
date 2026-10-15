---
name: audit
description: "Audits the branch that implements a spec and its tickets in a fresh context, along three axes: faithfulness to the originating spec, conformity with the project's conventions, and refactoring opportunities, then reports every finding with its evidence. Use when the user wants a branch checked against its originating spec and tickets along any of the above axes."
context: fork
background: false
---

Audit the changes that implement a **spec** and its **tickets**, and report what is wrong with them. The audit only finds issues, it is the caller's ownership to decide how to deal with them. You run without the conversation that produced the code, so that none of its assumptions shape what you see: rely only on the spec, the tickets, and the codebase.

## Scope

Identify the originating spec: issue references in the commit messages, fetched from the project's issue tracker, or the issue passed as an argument. Read their full body and comments, then those of each of its tickets, the spec's sub-issues.

The **change** is everything on the current branch since it forked from the default branch, uncommitted work included: the diff between the merge-base of the two and the working tree. Get the merge-base and the diff with two separate commands, and tell subagents to read files with Read and Grep: a `$(…)` substitution or a chained shell command needs approval nobody is there to give.

## Axes

Audit the change along three **axes**, each in its own general-purpose subagent, all started in the same turn, so that no axis's reasoning pollutes another's. Start them in the foreground and wait for all three to finish.

Subagents don't see this conversation, so give each one a self-contained brief with guidance on:
- how and where to read the spec and its tickets.
- how to report findings (see the paragraph and section below).
- its axis section below.

Each axis reports every issue it finds as a **finding**: the `file:line` it concerns, the evidence it rests on, quoted verbatim, and what is wrong with the current implementation. Each axis reports only what its own section defines, since the other axes cover the rest.

### Faithfulness

The change is faithful when it implements every decision and acceptance criterion of the spec and its tickets as stated, and builds nothing they don't ask for or that the spec puts out of scope. Report every way the change falls short of that, quoting the spec or ticket section that shows it.

### Conventions

Find out how this project expects its code to be written, from wherever it documents it, and report every place where the change departs from that, quoting the rule it breaks. The documentation is always the authority: code drifts away from it, so a change that contradicts a documented rule is a finding even when existing code does the same.

Where the documentation is silent, the existing code sets the convention by being consistent. Report a change that departs from such a pattern, citing the existing code that establishes it.

### Refactoring

Report every place where the code the change adds or modifies could be simpler or better structured: for example, logic duplicated within the change, existing code it could have reused, or indirection nothing needs. Reference the code involved and leave code the change doesn't touch alone.

## Report

Before reporting, open each finding's cited location and quoted source yourself, and drop the finding if they don't say what it claims, stating how many you dropped.

Present the reports under `## Faithfulness`, `## Conventions`, and `## Refactoring` headings. Keep the axes apart: never merge, deduplicate, or rank findings across them, since a change can pass one axis and fail another, and a single list would let one hide the other.
