---
name: implement
description: Implements a spec published by specalize, with its tickets, in one autonomous run. Implementer subagents build tickets test-first in parallel worktrees, as soon as their blockers land on a single integration branch, then the whole is reviewed and closed through one pull request.
disable-model-invocation: true
---

Implement a **spec** and all of its **tickets** in one run, on a single **integration branch**. You are the **orchestrator**: you never write code yourself. **Implementer** subagents build the tickets, each in its own worktree, as many at once as the tickets' blocking relationships allow. The [Implementers](#implementers) section is their instructions, not yours.

The tickets are not a list of steps but a **task graph**: a ticket can start as soon as every ticket **blocking** it has landed on the integration branch. The open tickets that can start form the **frontier**. Work it out from what the integration branch contains, never from the tracker, whose blocked-by state only changes when work is closed.

## Orchestration

1. Read the spec: the issue passed as an argument, otherwise the spec published earlier in this session; when there is neither, ask for it. Read its full body and comments, then its tickets, the spec's sub-issues, with the tickets blocking each, until you understand the task graph.
2. When the tickets need exploration of the codebase or of external documentation, run one **exploration** subagent first. It saves its findings as markdown notes in a directory outside the repository, accessible by all future subagents. This lets implementers focus on implementation rather than exploration.
3. Create the integration branch from the tip of the default branch, named `<spec number>-<spec title slug>`, and check it out. Then start one implementer for every ticket on the frontier, all in the background and each with `isolation: "worktree"`.
4. Each implementer builds its ticket following the [Implementers](#implementers) section. When one reports its ticket done, fast-forward the integration branch to the implementer's branch. Every landed ticket can change the frontier: start an implementer for every ticket that joined it.
5. After the first ticket lands, if the tracker closes work through pull requests, push the integration branch and open a draft pull request that closes the spec and every ticket. Push again after every later landing.
6. When an implementer reports it is stuck, put its ticket and every ticket that depends on it on hold, let the other implementers carry on, and put the question to the user right away. When they answer, resume the same implementer with the answer.
7. Once every ticket has landed, call the Skill tool with `skills:review` on the integration branch, comparing it against the default branch and checking it against the spec. Hand all of its findings to a single implementer, started and landed like a ticket's.
8. If a draft pull request exists, push and mark it ready for review. Otherwise, close each ticket and the spec the way the tracker closes work. Report the integration branch, and remove any implementer worktrees that are left.

Communicate with subagents through **context pointers**: links to the spec and the ticket, the path to the exploration notes, the integration branch, and earlier commits. Never duplicate what a pointer already gives them.

## Implementers

1. Before anything else, reset your branch onto the integration branch if your worktree isn't based on it already.
2. Read the ticket, the spec, and the exploration notes when there are any. Read the project's `GLOSSARY.md` files and use only their terms throughout the implementation of the ticket.
3. When the ticket adds or changes behavior, build it test-first, following [Test-driven development](#test-driven-development). Build any other ticket, such as configuration, wiring, prefactoring, or a wide refactor, directly: its acceptance criteria and the existing tests are its check.
4. Never refactor code beyond what the ticket needs: refactoring belongs to the review that follows the last ticket. While you work, run only the test files you touch, and run typechecking each time they pass.
5. When the ticket is done, commit it as one commit titled after the ticket and referencing its issue. Rebase onto the tip of the integration branch, resolving any conflicts, then run the full test suite and typechecking.

When the ticket is ambiguous, needs a decision the spec doesn't make, such as the shape of an interface, or has a test you can't make pass, stop and report the question instead of answering it with a default of your own.

### Test-driven development

A good test verifies behavior through a public interface, never implementation details: it reads like a specification of what the code does and it keeps passing when the code behind the interface is rewritten.

Tests live at **seams**: the places where you can alter behavior without editing in that place, where a module's interface lives and callers observe its behavior without reaching inside. Write tests only at the seams the spec records, never against internals.

Build the ticket in **vertical slices**, one behavior per cycle: write one test for the behavior and run it to see it fail for the reason you expect, then write only enough code to make it pass, without anticipating the next tests.

Write each test at the fastest seam that observes the behavior. Add an end-to-end test, such as a browser test, only when the ticket's acceptance criteria call for one, and only once the behavior it covers passes.

Avoid these anti-patterns:

- **Implementation-coupled tests**: a test that mocks the project's own modules, calls private functions, or checks its result through a side channel, such as querying the database instead of the interface. It breaks when the code is refactored even though the behavior didn't change.
- **Tautological tests**: a test whose expected value is computed the way the code computes it, so that it passes by construction. Take expected values from an independent source: a known literal, a worked example, or the spec.
- **Horizontal slicing**: writing all the tests first, then all the code. Tests written in bulk check the behavior you imagined rather than the behavior you built. Work in vertical slices instead: write each test only once the previous one passes, and let what that cycle taught you decide what it tests next.
