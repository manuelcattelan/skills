---
name: specalize
description: Turns the decisions made in this session into a spec, published to the project's issue tracker, that later agent sessions build from without seeing this conversation. Records only what the user already decided; it asks nothing and decides nothing.
disable-model-invocation: true
---

Turn what this session decided into a **spec**: an issue in the project's issue tracker that later agent sessions build from without ever seeing this conversation. Don't interview the user and don't make decisions: the spec only records what the user already decided, and anything it states that the user never decided is a defect.

1. Explore the repository to understand the current state of the codebase if you haven't already and use the project's domain vocabulary from its `GLOSSARY.md` files throughout the spec.
2. Give each decision its reasoning: the reason the user gave, or the reasoning of the recommendation they accepted. Record the implementation decisions the thread made too, but leave out file paths, code snippets, and other references to the codebase unless the decision itself is about one.
3. Record the **seams** the work will be tested at: the places where you can alter behavior without editing in that place, where a module's interface lives and tests observe its behavior without reaching inside. Prefer existing seams to new ones and the highest seam possible, and keep them as few as possible.
4. Write the spec following the template below, leaving out any section with nothing to put in it, then publish it to the project's issue tracker.

```md
# <what is being built, in the glossary's terms>

## Problem
<the problem as the thread described it, from the perspective of whoever has it>

## Solution
<the shape of the solution, from the same perspective, stating nothing the decisions don't>

## Decisions
### <area>
- **<decision>**: <its reasoning, including the facts it rests on>

## Out of scope
- <what will not be built>: <why it was left out of this spec>

## Open questions
- <question>: <why it is still open: unanswered, or set aside by the user>
```
