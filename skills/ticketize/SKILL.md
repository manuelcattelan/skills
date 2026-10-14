---
name: ticketize
description: Breaks a spec published by specalize into tickets, tracer-bullet vertical slices that each declare the tickets blocking them, and publishes them as sub-issues of the spec once the user approves the split, so that later agent sessions can implement them in dependency order without seeing this conversation.
disable-model-invocation: true
---

Break a **spec** into **tickets**: sub-issues of the spec that fresh agent sessions implement, without ever seeing this conversation, as soon as the tickets **blocking** them are done.

1. Read the spec: the issue passed as an argument, otherwise the spec published earlier in this session; when there is neither, ask for it. Read its full body and comments.
2. Explore the repository to understand the current state of the codebase if you haven't already, and use the project's domain vocabulary from its `GLOSSARY.md` files throughout the tickets.
3. Draft the tickets following the rules below, then review them with the user until they approve the split.
4. Publish the approved tickets to the project's issue tracker.

## Tickets

Make every ticket a **tracer bullet**: a narrow but complete path through every layer the change touches, which its own acceptance criteria can verify as soon as it lands. Never slice by layer.

Size each ticket so that a fresh agent can implement and verify it while most of its context window is still free, before its reasoning degrades, and keep the tickets as few as that allows. When the whole spec fits in one such ticket, propose a single ticket.

A ticket's **blockers** are the tickets that must be done before it can start. List only the ones that really gate it.

Two kinds of tickets are not tracer bullets:
- **Prefactoring**: when existing code would make a slice hard to build, restructure it first, without changing behavior, in its own ticket that blocks the slices relying on it.
- **Wide refactors**: when one mechanical change, like renaming a field everywhere, breaks too much code to land at once, split it into tickets that each keep everything working.

Every ticket must be implementable by an agent alone: it names the spec decisions it implements and decides nothing the spec doesn't. When a ticket would need a human, to make a decision or to answer one of the spec's open questions, hold it back and raise it at review. Never answer a question you raise with a default of your own, even one the user could override.

Write each acceptance criterion as an observable check that fails at the commit the implementer starts from, where every blocker is already done, and that this ticket's work alone makes pass.

Refer to the codebase loosely, by package, module or area, and only when the ticket needs to say where it acts. Leave out file paths, line numbers, code snippets and other references that go stale quickly, unless the decision itself is about one.

## Review

Present the tickets as a numbered list. For each ticket, give its title, the tickets blocking it, and what it delivers. After the list, raise every decision and open question a held-back ticket waits on.

Then ask the user for feedback on the split, for example:
- Is each ticket of the right granularity? Is it too coarse or too fine?
- Is each ticket blocked only by the tickets that really gate it?
- Should any tickets be merged or split further?

Revise it and present it again after every change, and publish nothing until they explicitly approve it: feedback is not approval. When the user answers an open question, the held-back tickets join the split; when they set it aside, drop the tickets that wait on it.

## Publishing

First, move every open question the user answered from the spec's `Open questions` to its `Decisions`, with the user's reasoning. Change the spec in no other way.

Then publish the tickets blockers first, creating each one already linked: a sub-issue of the spec, blocked by its blockers, with the tracker's native relationships set in the same call that creates it whenever the tracker allows it.

When the tracker has no native sub-issues or blocking relationships, use the `Parent` and `Blocked by` sections of the template instead. Leave out any other section with nothing to put in it.

```md
# <the behavior this ticket makes work, in the glossary's terms>

## What to build
<the end-to-end behavior, from the perspective of whoever uses it>

## Decisions
- <area>: <decision>, for each spec decision this ticket implements

## Acceptance criteria
- [ ] <an observable check that fails before this ticket's work and passes after it>

## Parent
<link to the spec>

## Blocked by
- <link to each blocking ticket>
```

Finish by listing the published tickets with their links and blockers.
