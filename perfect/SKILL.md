---
name: perfect
description: Exhaustive, multi-phase audit that brings the codebase to a pristine state — enforcing conventions, verifying every third-party API call against current documentation, fixing logic bugs, and restructuring code for maximum clarity and separation of concerns. Does not stop until there is genuinely nothing left to improve.
---

Perform an exhaustive, multi-phase audit of the repository. If a path argument is
provided, scope the audit to that path — otherwise scan the entire repository.

"Perfect" means there is nothing left to improve. Not "good enough," not "no
violations" — nothing. Every dimension must be addressed:

- Convention compliance (CLAUDE.md, rules files, established codebase patterns)
- Third-party API correctness (verified against current official documentation)
- Code quality (DRY, no dead code, no unnecessary complexity)
- Naming consistency (same concept always has the same name everywhere)
- Structural consistency (file organization, import ordering, block ordering)
- Logic correctness (no bugs, no missing pieces, no broken flows)
- Separation of concerns (components, hooks, and utilities are focused and small)
- State placement (state lives only where it truly belongs)

If something can be cleaner, more separated, or more consistent — it is an issue.
There are no "minor suggestions" or "nice-to-haves." Everything is a required fix.
The user approval step before applying fixes is the safety valve, not your judgment
about whether something is "worth fixing."

Do not be hasty. Do not cut corners. Do not skip steps because they are slow or
tedious. Try very hard on every phase. Read every file. Fetch every doc. Check
every call. The skill is called "perfect" — act like it.

---

## Phase 1: Tooling (blocking)

Discover all available project tooling by inspecting package.json scripts,
Makefiles, or any other project configuration files. Run every relevant check
(linting, formatting, type checking, tests, builds).

If any tooling fails, report the failures and stop. Get approval, fix them, and
re-run all tooling. Do not proceed to Phase 2 until every tool passes. There is
no point auditing code that does not compile or lint.

## Phase 2: Convention and API audit

This phase has two sub-steps. Present findings from both as a single report, get
approval, then apply all fixes as one batch.

### 2a. Convention, consistency, and correctness audit

Read every file in scope. Check each file against all sources of truth:

- CLAUDE.md files, rules files, and user preferences from memory
- Established patterns and conventions already present in the codebase
- Software engineering principles (DRY, separation of concerns, readability)
- Naming consistency across the entire codebase
- Logic correctness (missing translations, broken flows, dead code, wrong types)

For each issue, cite the authoritative source that makes it a violation and
propose the fix. Always specify whether a violation originates from a local
source (CLAUDE.md, memory, rules files) so the user can determine if the issue
lies in the code or in the source itself.

### 2b. Third-party API verification

Identify every third-party library used in the codebase. For each one, use
Context7 MCP (resolve-library-id then query-docs) to fetch current official
documentation. Do not rely on training data alone — documentation changes.

Verify that every API call, import path, function signature, and parameter shape
in the codebase matches the fetched documentation. Report any mismatches,
deprecated patterns, incorrect parameter names, or non-canonical import paths.

Use agents to parallelize documentation fetching across libraries. Do not skip
libraries because there are many. Do not assume a library is "probably fine."
Check every single one.

### After fixes

After applying approved fixes, re-run all project tooling to confirm nothing
was broken. If tooling fails, fix and re-run before proceeding.

## Phase 3: Structural improvements

Identify every opportunity to improve the structural clarity of the code:

- Components that do too many things and should be split
- Logic that should be extracted into custom hooks
- Repeated patterns that should become shared utilities
- State that lives in the wrong component
- Files that mix concerns

For each opportunity, propose a concrete plan: what to extract, where it goes,
how the pieces compose together. Do not just say "this should be split" — show
the architecture.

Present structural improvements as a separate report from Phase 2. These are
higher-impact changes and the user should have the chance to discuss and approve
them individually before implementation.

After implementing approved structural changes, re-run all project tooling. Any
new code generated (new files, new hooks, new components) is immediately subject
to the same audit standards as every other file — it must follow all conventions,
naming rules, and structural patterns. Do not introduce code that would fail your
own audit.

## Phase 4: Final clean pass

Re-scan the entire scope across all dimensions:

- Full convention and consistency check
- Full third-party API verification (re-fetch docs from Context7)
- Full structural review
- Full tooling run

Verify that all fixes from prior phases were correct and did not introduce new
problems or stem from hallucinated reasoning. If a previous fix was wrong, flag
it explicitly with an explanation of why and propose a corrected approach — do
not silently fix your own mistakes.

Only declare the repository in perfect state if this final pass surfaces zero
issues across every dimension and all tooling passes.

---

## Honesty

Be fully honest and realistic in your assessment. Do not minimize problems. Do
not be overly pleasing. If something is wrong, say it plainly. If something can
be better, say that too. The user wants the truth, not comfort.
