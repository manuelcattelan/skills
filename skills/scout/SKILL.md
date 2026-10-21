---
name: scout
description: Plans an effort too large for one interview session, such as a large refactor or a feature known only at a high level, as a map of trails on the project's issue tracker, then follows one trail per session, with the user or with research, until everything a spec needs is decided and specalize can write it from the map.
disable-model-invocation: true
---

Plan an effort too large for one `skills:interview` session as a **map**: an issue on the project's issue tracker whose sub-issues are **trails**, each covering one domain or angle of the effort. Scouting decides and never builds.

Sessions follow the trails one at a time until nothing is left to decide, and every session starts without the conversations of earlier ones, so the tracker is the only memory: record there everything a later session needs.

How to run the skill depends on the argument provided: for a map, or a trail of a map, see [Follow a trail](#follow-a-trail), on that trail when one is given. For anything else, see [Draw the map](#draw-the-map) for the effort it describes. With no argument, ask the user which.

## Map

The map is an issue labelled `scout:map`. Its body is an index, not a store: a decision lives only in the resolution of the trail that made it, and the map gives a reference link to each sub-issue trail for future readers.

Follow this template precisely when writing a map:

```md
## Problem
<the problem the effort solves, from the perspective of whoever has it>

## Destination
<what the spec will cover once every trail is resolved>

## Decisions so far
- <decision>: [<trail title>](<trail link>)

## Not yet specified
- <a question the effort will have to answer but that can't be stated precisely yet>: <what it waits on>

## Out of scope
- <what the effort leaves out>: <why>
```

- `Not yet specified` is the **fog**. A question leaves it and becomes a trail as soon as it can be stated precisely, even while it's blocked: whether it can be answered yet doesn't matter.
- `Out of scope` holds only what the whole effort leaves out, never what a session set aside to think about separately.

## Trails

A trail is a sub-issue of the map, labelled with its type and blocked by the trails whose answers it needs. Its body states the domain or angle it settles, the decisions it is expected to cover, and the context needed to start without reading anything else.

There are two types of trails:
- **Interview trail** (`scout:interview` label): resolved with the user, in a session that runs `skills:interview` on the trail.
- **Research trail** (`scout:research` label): facts a decision waits on, resolved without the user. See [Research](#research).

Wherever possible, keep everything about one domain or angle in one trail, sized so that its interview fits in one session without its context window entering the "dumb zone". Split off a part of the trail only when you foresee it can't be settled in the same session as the rest. A trail with a single decision in it belongs in another related trail whenever possible.

A trail is **claimed** when it's assigned, which happens before any work on it starts so that sessions running in parallel skip it, and **resolved** when it's closed with a **resolution** comment. The **frontier** is the open trails whose blockers are all resolved and that nobody has claimed.

## Draw the map

1. Run `skills:interview` on the effort, covering only its problem, its destination, and what it leaves out: which areas the spec will cover, never how any of them will work, since settling that is what the trails are for.
2. Sweep the rest of the effort breadth-first, listing the decisions it needs without settling any of them. If the whole effort fits in one interview session, don't draw a map: stop and offer to continue it as a plain `skills:interview` session.
3. Draft the map and its trails: present each trail with its title, type, questions and blockers, followed by the fog and what is out of scope. Revise and present the draft again after every change request until the user explicitly approves it: feedback is not approval.
4. Publish the map, creating any missing `scout:` labels first, then its trails, blockers first, each created as a sub-issue of the map with its blockers already set.
5. Start the research trails, then finish with the map's link and its frontier.

## Follow a trail

1. Read the map's body and its open trails, then start every research trail on the frontier. For each research trail that is claimed but still open, ask the user whether to restart it: only they know whether the session running it is still alive.
2. Use the trail given as an argument, otherwise present the frontier's interview trails, recommend one, and wait for the user to pick. Finally, claim the trail.
3. Read the trail and the resolutions of the trails blocking it. Read any other trail only when a question touches it. After that, run `skills:interview` on the trail. When the interview outgrows the session, settle what you can, and split the rest off as in step 5.
4. Post the resolution, close the trail, and add each of its decisions to the map's `Decisions so far`, linked to the trail:

   ```md
   ## Decisions
   - **<decision>**: <its reasoning, including the facts it rests on>
   ```

5. Update the originating map with what the session finds:
   - Route every question set aside or left outside the trail to an open, unclaimed trail on the same domain or angle, otherwise to a new trail blocked by what it waits on, otherwise to the fog when it can't be stated precisely.
   - Turn the fog that the resolution made precise into trails, and remove it from `Not yet specified`. Close a trail that now sits beyond the destination by adding it to `Out of scope`. Rewrite or close a trail that the resolution made obsolete.
   - When the session undermines a decision a resolved trail made, reopen that trail with a comment saying why. The trails it blocks wait on it again, so recommend it first whenever the frontier is presented.
6. Start any research trail this session created. Finish with the map's new frontier, or, when no trail is open and the fog is empty, tell the user to run `/specalize <map>` to proceed with creating a spec.

## Research

Start a research trail by claiming it and starting a background subagent that runs `skills:research` on the trail, posts the report as the trail's resolution, and closes it, even when part of the report is `Unverified`: the interviews that build on it treat an unverified fact as still open, not as settled.

The subagent never edits the map, so that concurrent edits can't overwrite each other. Subagents don't see this conversation, so give each one the links to the map and the trail, and tell it to run any subagents of its own in the foreground: it stops for good when its turn ends, so results still arriving in the background would never reach it.

Before a session ends, present its result, then wait for every research subagent it started. Add each trail they resolved to the map's `Decisions so far`, starting with `Facts:` since it records findings rather than a decision, and report which trails they resolved.
