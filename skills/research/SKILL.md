---
name: research
description: "Required first step whenever a task will write or change code, or propose a design, that uses a library, framework, API, or tool, even one you know well and even when the user did not ask for research: checks the planned approach against official, high-trust sources for the version the project uses, before any code is written. Also use when the user wants something researched, verified, or checked against the docs, asks a research question, or asks whether an approach is right, current, or idiomatic."
---

Base the work on what official, high-trust sources say rather than on what you remember. Hand the reading to research subagents, then report what the sources say about each of your claims.

## Claims

Before reading anything, write down what you currently believe as explicit, checkable **claims**, even when the task is an open question. That way the sources test your assumptions instead of being read through them.

Write a claim for everything a design or implementation choice rests on. For each choice that uses a library, framework, API, or tool, also claim that it is the documented, conventional way to do it, even when another way would work. Leave out claims already checked in this session and passing remarks that no choice rests on.

## Research

Group the claims by independent topic and start one research subagent per topic, all in parallel. Subagents don't see this conversation, so give each one a self-contained brief with the claims it should check, the decision each claim supports, the relevant code, and the versions the project uses, read from its lockfile, manifest, or installed packages. Tell each subagent to do the reading itself instead of starting this skill again.

Each subagent checks every claim against the version the project uses. It relies only on high-trust sources: official documentation, specifications, source code, changelogs and release notes, posts by the maintainers, and well-known references. It never uses Q&A sites, personal or aggregator blogs, or other unofficial content, not even as a lead. When a documentation tool is available, it prefers that tool over general web search, but doesn't stop there.

Until a claim's verdict is back, don't build on it. Work that doesn't depend on a pending claim can go on.

## Report

Once every subagent has returned, merge their results into a single report, have it checked following [Review](#review), then present it in the conversation, in this format every time. Leave out empty sections.

```md
### Contradicted
- **<claim>**: <corrected statement>
  [<source>](<link to the exact page or line>): "<verbatim quote that settles it>"

### Unverified
- **<claim>**: looked in <where you looked>

### Confirmed
- **<claim>**
  [<source>](<link to the exact page or line>): "<verbatim quote that settles it>"

### Also relevant
- **<finding>**: <the decision it changes>
  [<source>](<link to the exact page or line>): "<verbatim quote>"
```

Back every entry outside `Unverified` with a link and a quote copied word for word from the source, never a paraphrase, and leave out any finding you can't back that way. Mention a version only when the best source for a claim covers a different version than the one the project uses. Add a finding to `Also relevant` only when it changes a design or implementation decision, even though none of your claims asked about it.

## Review

Subagents can misquote a source, cite a page that doesn't say what they claim, or stretch a finding beyond what the source supports. Before presenting the report, start one review subagent with the merged report, the claims, and the project's context.

For every entry outside `Unverified`, the reviewer follows each claim's reference and checks that the source really says what the claim states and that the claim reports it faithfully, neither stretching nor narrowing its meaning.

Correct each entry that fails when what the reviewer found settles it, and move it to `Unverified` otherwise, saying what failed. Review the report once: don't send the corrected report back for another review.

## After the report

When a claim is `Contradicted`, correct your own work from this session to match the sources, plans and code alike, and say what you changed. When the correction would touch something the user explicitly decided or asked for, or code that existed before the session, stop and ask instead.

When the user asked a question rather than for a change, end by offering to save the findings as documentation, in the place where the repo already keeps such notes.
