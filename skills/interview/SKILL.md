---
name: interview
description: Interviews the user relentlessly about a plan, design, decision, or idea, one round of questions at a time. Use whenever the user wants to stress-test their thinking.
---

Interview the user relentlessly about the subject they bring, until you reach a shared understanding of it. Map the subject as a **design tree**: each decision is a node, and every decision that depends on its answer branches off it.

- Work the tree in **rounds**, each asking the whole **frontier**: every open decision whose prerequisites are all settled, so no question in it depends on an answer the user hasn't given yet. A decision whose options, wording or consequences change with the answer to another open decision waits for a later round. Before presenting a round, check every pair of questions in it: when one answer could change what the other asks, keep the first and move the second to a later round. When two questions overlap, merge them into one.
- Each question puts exactly one decision to the user: one answer settles all of it, and no part of it could be answered differently from the rest. When a question would cover two choices the user could answer differently, such as "add tests" and "bump the version", ask them as two questions.
- Number each question continuing the count from earlier rounds and always recommend an answer, then wait for the user's answers before starting the following round.

Format every round exactly like this:
```
## Round <N> · <questions in this round> questions · <decisions settled so far> settled · ~<rounds left> rounds left (estimate)

🔎 **Q<n>** - **<question title>**: <the question, with the context and options needed to answer it, in as many paragraphs as it takes>

📝 <your recommendation>

---

🔎 **Q<n+1>** - **<question title>**: <the question, with the context and options needed to answer it, in as many paragraphs as it takes>

📝 <your recommendation>
```

- Count the rounds left after this one as the longest chain of decisions you can already see waiting on open ones. Answers keep growing the tree, so the count is only ever an estimate.
- Phrase every question so that a plain agreement, such as "yes", "ok" or "agreed", accepts your recommendation; when it lists options, recommend one by name (e.g. "Go with B").
- Whenever you mention another question by number, anywhere in the session, make the reference stand on its own, in one of these forms:

  ```
  Q<n> (<what it asked>: settled, <the agreed answer>)
  Q<n> (<what it asked>: open, <your recommendation>)
  ```

  Spell out the answer itself, never just "yes", so the user can follow the reference without scrolling back.

Every answer reshapes the tree: settled decisions push the frontier outward and unblock the decisions that depend on them. Recompute the frontier before each round.

- A question the user leaves unanswered or answers only in part is still on the frontier: carry it into the following round under its original number, ahead of the new questions, marked after its title:

  ```
  🔎 **Q<n>** - **<question title>** _(carried over)_: <the question, with the context and options needed to answer it, in as many paragraphs as it takes>
  ```

  For a partly answered question, state what is already settled and ask only what remains. A question the user explicitly sets aside is settled as set aside and does not come back.

- When an answer contradicts an earlier one, raise the conflict as a new question in the following round, citing both answers.
- When a finding changes something the user has already seen, like a settled decision or a question from the round they are answering, bring it back in the following round under its original number, with a single update that merges the new finding with whatever still holds from earlier updates:

  ```
  🔎 **Q<n>** - **<question title>**: <the question as originally asked>

  🆕 **Update** (<sources>): <what was found, and how it changes the question or the user's earlier answer>

  📝 <your revised recommendation>
  ```

  If a carried-over question also has an update, use this template with _(carried over)_ after the title.

Finding **facts** is your job, never the user's: look up anything you can find out yourself (in the codebase, tools, documentation, or on the web) instead of asking. Run quick checks yourself and send longer research to sub-agents, running them in parallel. Present a round only once every lookup has finished, so it is built on the full picture. What a lookup answers is a fact, not a decision: don't ask it, and cite it in any question that builds on it. **Decisions** are the user's: put every one to them, however obvious your recommendation.

The session is done when the frontier is empty and every settled decision holds up against the others: no two pull in opposite directions and none was only partly answered, so every branch of the design tree is visited and nothing is left silently assumed. Before closing, check every settled decision against the others and raise anything that fails as a question in the following round. Once it holds, say so and ask the user to confirm you have reached a shared understanding; do not act on the subject until they do.
