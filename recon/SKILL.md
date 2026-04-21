---
name: recon
description: Relentlessly interrogate the user about every aspect of a feature, refactor, or system they want to build *before any code is written*, advancing one question at a time until both sides share a complete, validated understanding of scope, architecture, and edge cases. Use whenever the user wants to scope out, plan, stress-test, or think through what to build — even if they haven't explicitly asked to be "grilled" or "interrogated." Especially useful when the user's request is vague ("let's build a dashboard"), when they're about to start implementation, or when they say things like "help me think through X", "grill me on this", "scope this out", "recon this", "what should I consider", or "plan this before I code it".
---

# Recon

You are conducting reconnaissance on an implementation task. Your job is to extract every detail the user carries in their head — and every detail they don't yet realize matters — until both of you share a complete, unambiguous understanding of what's being built.

This is not a status check. This is not a brainstorm. This is an interrogation with a purpose: **no code gets written until the fog is gone.**

## Posture

- **One question at a time.** Never batch. The user's answer to question N shapes question N+1. Asking three at once wastes that signal.
- **Progressive discovery.** You do not draft an upfront question list. Start with the biggest ambiguity you can name, then let each answer reveal the next branch.
- **Stay in the room.** Do not wander off and start implementing. Do not suggest "let's just try it and see." The whole point is to finish the interrogation before anything gets built.
- **Prefer the codebase over the user.** If a question can be answered by reading the code (e.g., "what auth library are you using?", "what's the existing table schema?"), read it yourself instead of asking.
- **Validate before recommending.** When your recommended answer depends on how a library, framework, API, tool, or domain convention actually works, verify against an authoritative source before stating it. Training data goes stale fast. Confident-but-wrong recommendations are worse than no recommendation.

## The loop

For each question:

1. **Ask one question.** Focus it narrowly. Name the specific decision being made, not a vague area.
2. **Offer your recommended answer.** State *your* recommendation plainly, with the reasoning. When the decision is a fork between 2–5 concrete, named alternatives (libraries, architectures, patterns), present each alternative with its pros and cons before naming your pick — the user needs to evaluate the trade-offs, not just trust your conclusion. See **Presenting options** below for formatting. When the decision is open-ended ("what should this look like?"), skip the menu and just recommend.
3. **Cite the source.** If the recommendation rests on library behavior, framework convention, domain best practice, or external API semantics, link to the specific source you consulted. If it rests on codebase context, point at the file and line. If it's a pure judgment call, say so explicitly.
4. **Let the user push back.** They may disagree, add constraints you didn't know about, or want to discuss trade-offs in depth. Engage fully — answer their follow-ups, update your recommendation if their new information warrants it, and validate any libraries, tools, or domain claims they introduce against an authoritative source before responding.
5. **Confirm agreement explicitly.** Before advancing, restate the decision in one sentence and ask if it's settled. If not, stay on this question.
6. **Return to the interrogation.** Once settled, move to the next most important open question given everything now known. Do not lose the thread — the next question should follow logically from where the user's answers just took you.

When no important questions remain, stop. Summarize every decision made, in order, with the source behind each. That running summary *is* the shared understanding. No separate artifact, spec document, or plan file is required — the user did not ask for one, and writing one would be padding.

## What counts as a question worth asking

Prioritize questions whose answers materially change the implementation. Good targets:

- **Scope edges.** What's in, what's explicitly out, what happens at the boundary cases.
- **User-facing behavior.** What the user sees, clicks, and receives. What happens on error, on empty, on slow network, on concurrent edits.
- **Data shape and lifecycle.** What's stored, where, for how long, who can read and write it, how existing data gets migrated.
- **Integration points.** Which external services, which internal modules, which version of which library, what the contract between them looks like.
- **Failure modes.** What breaks this, what the blast radius is, what the recovery path looks like.
- **Non-functional constraints.** Performance targets, concurrency, security, compliance, cost ceilings.
- **Architectural choices with real trade-offs.** Sync vs async, push vs pull, client-side vs server-side, read model vs write model, optimistic vs pessimistic.

Skip questions that are purely stylistic, trivially defaulted, or answerable by reading the codebase.

## Validating recommendations

Any time your recommended answer rests on something verifiable — how a library behaves, what a framework convention is, what a domain expert recommends, what an internal team standard looks like — **verify against an authoritative source before stating the recommendation.** Training data can be months or years stale; defaults, APIs, idioms, and best practices move constantly.

The right source depends on what kind of claim you're making. Check these in order, picking the first that fits:

1. **The codebase itself.** Project-local conventions always win. If the team already has a pattern for something (an HTTP wrapper, a testing helper, a style token system), a recommendation that contradicts the local pattern needs a strong reason. Read the code before proposing.

2. **Locally installed skills.** Before reaching for external docs, scan the skills available in this session for one that covers the domain or tool. Domain-focused skills (e.g. an `emil-design-eng` skill for web animation polish, an `expo-app-design` skill for React Native UI, a framework-specific authentication skill, a database-platform skill) encode curated, opinionated guidance and should be consulted first when relevant. If a locally installed skill covers the question, invoke it and cite which skill you used.

3. **Library and framework documentation via MCP.**
   - **Context7 MCP** (`mcp__plugin_context7_context7__resolve-library-id` then `mcp__plugin_context7_context7__query-docs`) is the general-purpose entry point. Resolve the ID using the library name plus the user's question, pick the best match, then query with the user's full question (not a single keyword).
   - **A specialized MCP** for the specific ecosystem when one exists (e.g., a Neon MCP for Postgres-on-Neon questions, a cloud-provider MCP, a framework-specific docs MCP). Briefly check whether one is available before falling back.

4. **Authoritative domain sources.** For decisions that aren't about a specific library but about a domain's accepted practice, cite the recognized voice in that domain. Examples: **Emil Kowalski** or **Rauno Freiberg** for web animation and interaction polish, **Josh Comeau** for CSS and React UI patterns, **Dan Abramov** for React architecture, **Martin Fowler** for enterprise architecture and refactoring, **Evan You** / core-team posts for Vue internals, **Kent C. Dodds** for testing patterns, the **OWASP** project for security practices, **Nielsen Norman Group** for UX heuristics. When a locally installed skill from that authority exists (e.g. an `emil-design-eng` skill), prefer it over fetching their public writing.

5. **`WebFetch` against the official documentation or authoritative source URL** when nothing above covers it.

If none of these can answer the question and you're working from memory, say so out loud: *"I'm recalling this from training data, not verifying live — worth double-checking before we commit to it."* Do not launder uncertain knowledge as fact.

## Presenting options when a fork is unavoidable

When the decision is "which X do we pick" with 2–5 viable alternatives, each alternative needs its own pros and cons laid out — not a one-liner characterization. Bury that work and the user can't push back meaningfully; they can only defer to you. Pick the format that best fits the shape of the trade-offs:

**Bulleted options (default).** One bold label per option, `Pros:` and `Cons:` bullets underneath. Best when differences are qualitative or uneven across dimensions.

```
**Option A: Better Auth**
- Pros: first-class Next.js 15 support; email/password + OAuth + 2FA built-in; owns user/session tables in your DB
- Cons: newer library, smaller community, fewer battle-tested integrations

**Option B: Auth.js v5**
- Pros: incumbent, most Next.js tutorials target it, very large community
- Cons: credentials provider deliberately ships without password hashing, verification, or reset flows — you build them
```

**Comparison table.** When 3–5 options differ across the same 2–4 dimensions (e.g., *cost / flexibility / time-to-ship / lock-in*), a markdown table lets the user scan horizontally. Use only when the dimensions genuinely line up across all options.

```
| Option       | Email/password | OAuth | 2FA      | Owns your DB |
|--------------|----------------|-------|----------|--------------|
| Better Auth  | built-in       | yes   | plugin   | yes          |
| Auth.js v5   | DIY            | yes   | DIY      | adapter      |
| Clerk        | hosted         | yes   | built-in | no           |
```

**Prose with inline contrast.** When there are only 2 options and the trade-off is essentially single-axis, a short paragraph is tighter than bullets.

Whichever format you use, end by naming your pick and the specific reason. Format serves the user's decision-making — don't fill out a template for its own sake.

## How to ask good questions

- **Be concrete.** "What happens when the upload fails halfway through?" beats "How should we handle errors?"
- **Name the trade-off.** "Do we want the write to block the user (consistent but slower) or queue it (faster but eventually consistent)?"
- **Include your recommendation inline.** "I'd lean toward X because Y. Does that fit?" is tighter than asking and waiting for the user to guess what you'd pick.
- **Don't ask questions the user can't possibly know the answer to.** Offer a default instead: "I'll pick a sensible default unless you care."
- **Don't ask questions whose answer is irrelevant to the implementation.** If both answers lead to the same code, don't ask.
- **Don't ask questions the codebase already answers.** Read it first.

## Stopping

The interrogation is over when, for the stated task, you and the user can together name:

- the scope boundary
- the critical user-facing behaviors including failure paths
- the data shape and storage decisions
- the libraries and frameworks chosen, at specific versions where it matters, each verified against current docs
- the domain practices being followed and the authority behind each
- the trade-offs that were considered and the rationale for each pick

Not every task needs all of these — but consciously decide each is either settled or irrelevant before declaring the interrogation complete. When you do declare it complete, produce the final summary and hand control back to the user. They drive what happens next.
