---
name: define
description: "Aligns with the user on the project's vocabulary, one term and one precise definition per concept, then records it in GLOSSARY.md files that agents follow. Use when the user wants to build or update the glossary, or to define, rename, or tell apart project concepts. Also use proactively, in any discussion of a feature or design, as soon as the user names a project concept with a vague or overloaded word, such as 'account' in a project that has both customers and logins: point out the ambiguity, propose the precise term, and offer to add it to the glossary."
---

Run the session with the `skills:interview` skill, putting each concept to the user as one question whose recommendation shows, in a fenced markdown block, the exact entry you would write into the glossary, `Avoid` and `Distinct from` lines included. Throughout the session, hold the user's language to the glossary:

- When they use a word from an entry's `Avoid` line, or use a term in a way its definition contradicts, point it out and ask which they mean.
- When they use a vague or overloaded word, propose the precise term.
- When the line between two concepts is unclear, invent a concrete case that sits on it and ask which concept it belongs to.

## Entries

Concepts come from the domain and from the project's architecture, never from its implementation details: an entry names no class, function, field, file, or data type, not even on its `Avoid` line, because the glossary must stay true when the code is refactored.

Be opinionated when choosing a term: prefer the word the project's users and domain experts use, and when they use none or several, the most specific word no other entry uses. Never choose a word solely because the code uses it, and put every other candidate on the entry's `Avoid` line.

Write each definition so that it settles, for any concrete case, whether that case is an instance of the term. Keep it brief and precise, mention the concept's relationships to other terms when that prevents a misreading, link every term that has its own entry, and leave out implementation details.

## Glossaries

The root glossary is the `GLOSSARY.md` at the repository root, and its terms apply to the whole project. When a directory has its own cluster of terms that nothing outside it uses, propose a context glossary for it: a `GLOSSARY.md` at the root of that directory, listed in the root glossary's `Glossary tree` section. Create it only after the user approves.

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

The `; replaced by …` part is left out when nothing replaces the concept. Only a spec retires a concept: never move an entry to `## Deprecated` in a session of your own.

## Glossary diffs

When `specalize` hands you a **glossary diff**, the glossary changes a spec's decisions require, put to the user only its items and the entries whose definition, lines, or links name a term it renames or deprecates. Once the user confirms the shared understanding, write nothing, not even the block in [Writing](#writing): return every agreed change to `specalize` as an item of its spec's `## Glossary` section, since the implementation writes them alongside the code they describe.

## Writing

Once the user confirms the shared understanding, write every glossary the session changed. Then add the block below to the `CLAUDE.md` and the `AGENTS.md` at the repository root, for each of the two that exists, replacing any existing `## Glossary` section; when neither exists, ask the user which one to create. Change nothing else: when code or documentation uses a word from an `Avoid` line, tell the user instead of renaming it, since those renames need their own review.

```md
## Glossary

`GLOSSARY.md` defines this project's vocabulary, and the glossaries listed in its `Glossary tree` section add the terms for their directories. Before you answer a question, explain something, or write code or documentation that involves a project concept, read them, and then use only their terms, even when the user or the code uses another word: when a word is on an entry's `Avoid` line, write that entry's term instead. Never use a term from a `## Deprecated` group in new code or documentation. When its `Deprecated` line names a replacement, use that term instead; when it names none, the concept no longer exists: leave the term out, and don't raise it or any remaining use of it with the user. When a concept you need has no entry, say so instead of coining a term.
```
