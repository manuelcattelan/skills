---
name: perfect
description: Comprehensive repository health check that iteratively audits and perfects the entire codebase against all conventions, user preferences, software engineering principles, and current third-party documentation until zero issues remain.
---

Perform a thorough, iterative audit of the repository to bring it to a pristine state. If a path argument is provided, scope the audit to that path — otherwise scan the entire repository.

On each iteration, read every file in scope and check it against all sources of truth available to you: CLAUDE.md files, user preferences from memory, rules files, established software engineering principles like DRY and separation of concerns, code readability and aesthetics, naming consistency, structural patterns, and correct usage of third-party libraries. For third-party libraries, always fetch current documentation from Context7 MCP or official llm.txt files and verify that every API call in the codebase matches it — do not rely on your training data alone.

Present your findings as a report grouped by scope and category. For each issue, cite the authoritative source that makes it a violation and propose the most elegant solution you found during research. Always specify whether a violation originates from a local source like CLAUDE.md, memory, or rules files so the user can determine if the issue lies in the code or in the source itself. Be fully honest and realistic in your assessment — do not minimize problems or be overly pleasing. If something is wrong, say it plainly.

After presenting the report, wait for user approval before applying any fixes. Once fixes are applied, re-scan the entire scope again. During this re-scan, also verify that fixes you applied in prior iterations were correct and did not introduce new problems or stem from hallucinated reasoning. If a previous fix was wrong, flag it explicitly with an explanation of why and propose a corrected approach — do not silently fix your own mistakes.

Repeat this cycle until you are confident that running another iteration would surface zero issues. Only then declare the repository in perfect state.
