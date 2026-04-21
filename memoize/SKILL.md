---
name: memoize
description: Capture the critical points and decisions from the current session into a markdown document inside `docs/` so the knowledge can be retrieved later without being re-derived. Use whenever the user wants to write up, save, document, archive, or preserve the outcome of a planning, research, scoping, /recon, or design discussion — whether they say "document this session," "save our decisions," "write this up," "capture what we discussed," "put this in docs," "memoize this," or anything similar. Especially important after a /recon or planning session so the shared understanding survives beyond the conversation.
---

# Memoize

Write the critical parts and decisions from the current session into a markdown file inside `docs/` in the current working directory, so the knowledge can be retrieved later without re-running the conversation that produced it.

You are a faithful scribe, not an editor. Your job is to capture what was actually said — not to summarize it into something cleaner, infer rationale that wasn't stated, or frame the discussion in a way the user didn't.

## Invocation

The user provides two things:

- **subfolder** inside `docs/` (e.g. `plan`, `research`, `design`)
- **filename** (without the `.md` extension)

The file is written to `docs/<subfolder>/<filename>.md`. Create the subfolder if it doesn't exist. If the user hasn't given you both pieces, ask for them — do not guess.

Do not introduce a taxonomy the user didn't ask for, and do not classify the session yourself. The subfolder is the user's call.

## Capture rules

These are the core of the skill. Violate them and the output is useless for later analysis — the reader can't tell what was actually agreed versus what you invented.

1. **Only include things that were explicitly discussed.** If it wasn't said in the session, it doesn't go in the document. Not even "helpful context" you think belongs there.
2. **No inferred rationale.** If a decision was made without a stated reason, write the decision and leave the rationale blank. Do not invent a plausible-sounding justification.
3. **No extrapolation.** Do not extend a point past what was actually said ("the user wanted X, so presumably also Y"). Stop at the edge of the transcript.
4. **Paraphrase is fine; invention is not.** You may condense wording and reorganize for readability, but every claim must be traceable to something someone actually said in the session.
5. **Omit rather than invent.** If a section would be empty or weak, leave it empty — or leave it out. An honest gap is more useful later than confident-looking filler.

If the session has nothing substantial enough to capture, say so and do not write a file.

## Structure

Use this template:

```markdown
# <title>

## Discussion
<the critical parts of what was discussed, as bullets or short paragraphs>

## Decisions
- <decision> — <rationale, if one was stated; omit the em-dash and rationale if none was given>
```

Sections may be empty when the session had nothing for them. A research session may have a rich `Discussion` and an empty `Decisions`; a quick decision session may have the inverse. That's expected — do not pad.

The `<title>` is the filename the user provided, spaced out and capitalized. Nothing invented.

## File collisions

Before writing, check whether the target file already exists. If it does, stop and ask the user whether to:

- overwrite the existing file,
- write to a different name, or
- cancel.

Do not append silently. Do not overwrite silently. These documents are meant to survive for later analysis — a silent clobber would destroy prior work.
