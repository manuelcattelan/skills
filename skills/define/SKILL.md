---
name: define
description: "Aligns with the user on the project's ubiquitous language, one term and one precise definition per concept, shared by the user, domain experts, code, and agents, then records it in GLOSSARY.md files that agents follow. Use when the user wants to build or update the glossary or the ubiquitous language, to review it for stale entries, or to define, rename, or tell apart project concepts. Also use proactively, in any discussion of a feature or design, as soon as the user names a project concept with a vague or overloaded word, such as 'account' in a project that has both customers and logins: point out the ambiguity, propose the precise term, and offer to add it to the glossary."
---

The glossaries record the project's [ubiquitous language](https://martinfowler.com/bliki/UbiquitousLanguage.html), the term Eric Evans uses in *Domain-Driven Design*: one language, settled with the people who know the domain, that conversations, specs, code, and documentation all use without translating. The glossary is its written record, and it drifts unless kept true to how the project actually speaks, which is what [Review](#review) is for.

Run the session with the `skills:interview` skill, putting each concept to the user as one question whose recommendation shows, in a fenced markdown block, the exact entry you would write into the glossary, `Avoid` and `Distinct from` lines included. Throughout the session, hold the user's language to the glossary:

- When they use a word from an entry's `Avoid` line, or use a term in a way its definition contradicts, point it out and ask which they mean.
- When they use a vague or overloaded word, propose the precise term.
- When the line between two concepts is unclear, invent a concrete case that sits on it and ask which concept it belongs to.
- When you propose a term, show the user a sentence that uses it to describe a concrete scenario, and when the sentence reads awkwardly, propose another term.

When the user asks to review the glossary, with `review` as the argument or in their own words, run the session following [Review](#review).

## Entries

Concepts come from the domain and from the project's architecture, never from its implementation details: an entry names no class, function, field, file, or data type, not even on its `Avoid` line, because the glossary must stay true when the code is refactored.

Be opinionated when choosing a term: prefer the word the project's users and domain experts use, unless it is ambiguous or too loose to settle every case; when they use none, several, or only such a word, choose the most specific word no other entry uses. Never choose a word solely because the code uses it, and put every other candidate on the entry's `Avoid` line.

Write each definition so that it settles, for any concrete case, whether that case is an instance of the term. Keep it brief and precise, mention the concept's relationships to other terms when that prevents a misreading, link every term that has its own entry, and leave out implementation details.

## Glossaries

The root glossary is the `GLOSSARY.md` at the repository root, and its terms apply to the whole project. When a directory has its own cluster of terms that nothing outside it uses, propose a context glossary for it: a `GLOSSARY.md` at the root of that directory, listed in the root glossary's `Glossary tree` section. Create it only after the user approves. A context glossary narrows the root glossary's language to its directory and never redefines a root term: the whole tree records one ubiquitous language.

Group the terms of every glossary under `##` headings named after the area of the domain they belong to, and order groups and terms alphabetically. Add the `Avoid` line only when other words compete for the concept, and the `Distinct from` line only when another concept is easily confused with it. A root glossary looks like this, without the `Glossary tree` section when there are no context glossaries:

```md
# Glossary

A shop that sells event tickets to the public.

## Glossary tree

- [Payments](app/payments/GLOSSARY.md): how Customers pay for Orders and get their money back.

## Sales

### Customer
A person or company that has placed at least one [Order](#order).
- **Avoid**: client, buyer
- **Distinct from**: [Visitor](#visitor): a Visitor has never placed an Order.

### Order
A Customer's request to buy one or more tickets, paid in a single payment.
- **Avoid**: purchase, booking
```

A context glossary looks like this:

```md
# Payments glossary

How Customers pay for Orders and get their money back. Terms in the [root glossary](../../GLOSSARY.md) also apply here.

## Refunds

### Chargeback
A payment reversed by the Customer's bank after the Customer disputes it, without the shop's involvement.
- **Distinct from**: [Refund](#refund): the shop starts a Refund.
```

When a spec retires a concept, its entry moves, unchanged, to a `## Deprecated` group at the end of its glossary, ordered alphabetically, with a `Deprecated` line linking the spec and, when another concept replaces it, that concept's entry:

```md
## Deprecated

### Ticket holder
A person who holds a ticket without having bought it.
- **Deprecated**: retired by [#42 Merge ticket holders into attendees](<spec link>); replaced by [Attendee](#attendee).
```

The `; replaced by …` part is left out when nothing replaces the concept. Once nothing in the code or documentation uses a deprecated term anymore, a spec can remove its entry; when the entry names a replacement, the term then goes on the replacement's `Avoid` line, so that agents keep avoiding it. Only a spec retires a concept or removes its entry: never move an entry to `## Deprecated`, or remove one from it, in a session of your own.

## Glossary diffs

When `specalize` hands you a **glossary diff**, the glossary changes a spec's decisions require, put to the user only its items and the entries whose definition, lines, or links name a term it renames, moves, deprecates, or removes. Once the user confirms the shared understanding, write nothing, not even the block in [Writing](#writing): return every agreed change to `specalize` as an item of its spec's `## Glossary` section, since the implementation writes them alongside the code they describe.

Items the user already agreed to earlier in the session, such as in a [review](#review) or an `/upkeep` session, are settled: return them without asking again, unless you find one plainly wrong, which you raise as a question of its own.

## Review

A **review** checks the glossaries against the code to catch entries gone stale: the main workflow only changes the concepts each effort names, so the rest drift unnoticed. It decides only whether the glossaries are right, and reads code and documentation only as evidence.

Whether code and documentation follow the glossaries is one of `/upkeep`'s checks, so leave to it every remaining use of a deprecated term or of a word from an `Avoid` line, and every mismatch where the glossary turns out to be right. A full review of the project needs both, this one first, since `/upkeep` takes the glossaries as its authority.

Review every glossary, or only the one the argument names after `review`, directly or through a directory it covers. Leave the `## Deprecated` groups alone: `/upkeep` checks them.

Check each glossary against the rules in [Entries](#entries) and [Glossaries](#glossaries), and against the code and documentation for:
- **Contradicted entries**: an entry whose definition, `Avoid` line, or `Distinct from` line the code or documentation contradicts. Ask the user which side is right, and when it is the glossary, drop the finding: fixing the code is left to `/upkeep`.
- **Unused concepts**: an entry whose concept nothing in the code or documentation realizes anymore, under its term or any other word. Ask the user whether to deprecate it.
- **Missing concepts**: a concept that the documentation or more than one area of the code names, that has no entry, and that [Entries](#entries) would give one.
- **Drifted words**: a word the code or documentation consistently uses for a concept, missing from its entry's `Avoid` line. Recommend adding it there, and renaming the entry only when the project's users or domain experts use that word.
- **Overlapping entries**: two entries that some concrete case fits both of. Recommend a `Distinct from` line that settles the case or, when the code no longer tells the concepts apart, a merge: one entry edited, the other deprecated with the first as its replacement.
- **Split concepts**: an entry that covers two concepts the code or documentation tells apart. Recommend an entry for each.

Check each glossary in its own general-purpose subagent, all started in the same turn, in the foreground: the root glossary against the whole repository, a context glossary against its directory.

Subagents don't see this conversation, so give each one a self-contained brief with its glossary, the code it covers, the [Entries](#entries) and [Glossaries](#glossaries) sections, and the checks above. Tell it to look for each concept by what its definition describes, not only by its term and `Avoid` words, and to report every **finding** with the entry or section it concerns, its check, and the evidence it rests on: `file:line` references, with the code or documentation quoted verbatim.

Before raising a finding, check that its evidence holds, and drop the ones whose evidence doesn't. Then put each finding to the user as one question, whose recommendation shows the change in a fenced markdown block: the entry as it would read, under `## Deprecated` for a deprecation. The evidence stays in the conversation: an entry never cites it.

Once the user confirms the shared understanding, write nothing to the glossaries, not even the changes they agreed to: those land through a spec like any other, so every glossary change is reviewed and audited the same way. Write only the block in [Writing](#writing), without telling the user about uses of `Avoid` words, which `/upkeep` covers.

When the user agreed to any change, finish by telling them to run `/specalize` in this same session, which records the changes in a spec's `## Glossary` section.

## Writing

Once the user confirms the shared understanding, write every glossary the session changed. Then add the block below to the `CLAUDE.md` and the `AGENTS.md` at the repository root, for each of the two that exists, replacing any existing `## Glossary` section; when neither exists, ask the user which one to create. Change nothing else, and don't report uses of `Avoid` words in code or documentation: `/upkeep` covers them.

```md
## Glossary

`GLOSSARY.md` defines this project's ubiquitous language, the one vocabulary that conversations, specs, code, and documentation all share, and the glossaries listed in its `Glossary tree` section add the terms for their directories. Before you answer a question, explain something, or write code or documentation that involves a project concept, read them, and then use only their terms, even when the user or the code uses another word: when a word is on an entry's `Avoid` line, write that entry's term instead. Never use a term from a `## Deprecated` group in new code or documentation. When its `Deprecated` line names a replacement, use that term instead; when it names none, the concept no longer exists: leave the term out, and leave any remaining use of it to `/upkeep` instead of raising it with the user. When a concept you need has no entry, say so instead of coining a term.
```
