---
name: perfect
description: Exhaustive, multi-phase audit that brings the codebase to a pristine state — enforcing conventions, verifying every third-party API call against current documentation, fixing logic bugs, and restructuring code for maximum clarity and separation of concerns. Does not stop until there is genuinely nothing left to improve.
---

Perform an exhaustive audit of the repository. If a path argument is provided,
scope the audit to that path — otherwise scan the entire repository.

"Perfect" means there is genuinely nothing left to improve — not "good enough,"
not "no violations," nothing. Before auditing anything, study the codebase to
understand what kind of project this is, then determine what perfection means in
this specific context. Research best practices for the frameworks and patterns in
use. The standards you enforce must be grounded in what actually matters here.

Do not be hasty. Do not cut corners. Do not skip steps because they are slow. Read
every file. Fetch every doc. Check every call. If something can be cleaner, more
separated, or more consistent — it is an issue, not a suggestion. The user approves
fixes before they are applied; that is the safety valve, not your judgment about
whether something is "worth fixing."

The audit proceeds in four phases, strictly in order.

**Phase 1 — Tooling.** Discover available project tooling by inspecting project
configuration. Run all relevant checks. If anything fails, report it and stop —
tooling must pass before the audit proceeds. Nothing else matters if the code does
not compile or lint.

**Phase 2 — Conventions and API correctness.** Read every file. Check it against
all sources of truth: CLAUDE.md, rules files, established codebase patterns, and
software engineering principles. Check for logic bugs, dead code, naming
inconsistencies, and broken flows. Then identify every third-party library and
verify every API call against current official documentation fetched from Context7
MCP — do not rely on training data, do not skip libraries because there are many,
do not assume anything is "probably fine." Present the full report, get approval,
apply fixes, re-run tooling.

**Phase 3 — Structural improvements.** Identify every opportunity to improve
structural clarity: units of code doing too many things, logic that would be
clearer if extracted, state living in the wrong place, mixed concerns. Propose
concrete plans showing what to extract, where it goes, and how the pieces compose.
Present these separately — they are higher-impact and should be discussed before
implementation. After applying approved changes, re-run tooling. Any new code
generated must pass the same audit standards as everything else.

**Phase 4 — Final clean pass.** Re-scan the entire scope across all dimensions.
Re-fetch third-party docs from Context7. Re-run all tooling. Verify that prior
fixes were correct and did not introduce new problems or stem from hallucinated
reasoning — if a previous fix was wrong, flag it explicitly. Only declare the
repository perfect if this pass surfaces zero issues.

Be honest. Do not minimize problems or be overly pleasing. If something is wrong,
say it plainly.
