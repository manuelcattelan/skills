---
name: prototype
description: "Builds a throwaway HTML prototype to settle a design question that talking can't: whether a state model or business logic behaves right, as a clickable demo driven by a pure logic module, or what a screen should look like, as structurally different UI variants rendered like the real app. Records the verdict on the project's issue tracker for the agents that implement it. Use when the user wants to see, click through, or compare options before deciding how something should look or behave, or when a question in an interview or a scout trail turns on that. Not for designs already settled, bugs in existing code, or demos of a whole application."
---

A **prototype** is throwaway code that answers one design question that talking can't settle: a state model with more edge cases than the user can hold in their head, or a screen nobody can picture until they see a few versions of it. The question decides what gets built, so a prototype that answers the wrong question is wasted however good it looks.

Every prototype is an HTML file built in a temporary directory outside the repository, and it never enters the repository: the decision reaches the code later, through a spec. What lasts is its **record**, which carries the decision to the agents that implement it.

You are the **orchestrator**: you never write the prototype yourself. A single **builder** subagent writes it and revises it, so that its code never fills this conversation. The [Builder](#builder) section is its instructions, not yours.

1. **Question**: pick the **kind** of prototype the question needs:
   - **Logic**: whether a state model, business rule, or data shape behaves right, which shows only once someone pushes it through real cases, including combinations of different scenarios.
   - **UI**: what a screen, flow or journey should look like from the user's perspective and how it should feel to use. Useful when whoever is driving the skill wants to explore and iterate over multiple options before committing.

   Before building anything, state the question in one sentence, its kind, and the plan: for logic, the actions and the walkthrough scenarios; for UI, how many variants and how each differs. When the skill runs on a question from an interview or a scout trail, take the question from there, then wait for the user to confirm: a prototype of the wrong question wastes the whole build.

2. **Build**: create a new temporary directory outside the repository, then start the builder: a general-purpose subagent, in the foreground. It doesn't see this conversation, so give it a self-contained brief about the question, the directory to build in, what it needs from this conversation and the codebase, with pointers to the relevant code and the project's `GLOSSARY.md` files, and the [Builder](#builder) section.

   When it returns, open the prototype for the user when you can, give its path, and guide them on how to run and review the prototype: the walkthroughs of a logic prototype, the variants of a UI, etc.

3. **Feedback**: the interesting moments are when the user says "wait, that shouldn't be possible", "huh, I assumed it would work differently", or "I want the header from B with the list from C": those are the problems in the idea, and finding them is the point.

   Send every change the user asks for to the same builder, resuming it with its context intact, such as with `SendMessage` and its ID, rather than starting a new one. Repeat until the user settles the question, which is the **verdict**. When the question turns out not to need a prototype anymore, stop without a record.

4. **Record**: write the verdict as a record that lets the agents implementing it work with the full context of the decision, without seeing this conversation or the prototype. Include everything they need and everything that helps them: the question and the verdict with its reasoning, but also, for example, the options rejected and why, the cases the prototype revealed, and the parts of the prototype that state the decision more precisely than prose, such as its logic module.

    When the skill runs on a scout trail, hand the record back to scout, which posts it as the trail's resolution. Otherwise, record it as an issue labelled `prototype:verdict` on the project's issue tracker, creating the label when it is missing, and close the issue as soon as it is created: the verdict is settled, so nothing about it is left to work on.

   Delete the temporary directory, then return the verdict with a link to its record, so that the interview or the spec that needed it can cite it.

## Builder

Build the prototype as a single self-contained HTML file in the directory you were given, never in the repository: plain HTML, CSS, and JavaScript, everything inline, so that it opens by double-click with nothing to install, build, or serve.

It is throwaway: no tests, no error handling beyond what keeps it running, no abstractions, and no persistence, with all state held in memory. Build only what answers the question, never what a later version might need. Use the terms of the project's `GLOSSARY.md` files for everything the user reads.

Before handing it over, check that the page loads and every control works without script errors, such as with a headless browser when one is available, so that the user's time goes to the question rather than to broken buttons. When you finish, and after every revision, report the file's path, what it lets the user try, and anything you assumed. When the brief or a request is unclear, ask through your report instead of guessing.

### Logic

Write the logic that answers the question as a small pure module in its own `<script>` block, shaped by the question: a reducer, a state machine when which actions are legal is part of the question, or a set of pure functions over plain data. Keep it free of the DOM: the page calls into it and nothing flows the other way, so that it can be lifted into the real code once the question is answered.

Write the page for someone who doesn't read code, such as a designer or a domain expert, with every label in domain language rather than code, laid out top to bottom:

1. **Context**: what the prototype explores, stating the question so that whoever is actually looking at the demo is always reminded of the initial problem statement and any relevant context around it.
2. **Current state**: the full relevant state as a panel of labelled fields, never a raw JSON dump, re-rendered after every action, with what just changed called out. This helps understand the parameters and settings that are being displayed inside the prototype at any given moment.
3. **Free play buttons**: one button per action, always available, so that anyone can push the model in any order and reach all available combinations of different scenarios and states. This acts directly on the current state of the prototype.
4. **Walkthroughs**: one view per scenario, each with a plain-language description of the situation and what to watch for, then its steps as buttons to press in order. Starting a walkthrough resets the state, so that it plays the same way every time. Cover the happy path, the tricky edge cases, and attempts at what should be impossible.

Keep its styling restrained, with clean typography, generous spacing, and one accent colour, so that nothing competes with the state and the buttons. Doing so makes it so that the UI doesn't get in the way of testing and reviewing the logic of the prototype.

### UI

Build 3 variants unless the plan says otherwise, never more than 5. Make them structurally different, in layout, information hierarchy, and primary affordance, never just in colour or copy: when two come out alike, redo one. Keep each variant's layout its own, sharing nothing but small components.

Make each variant as close to the real app as you can, since a mockup that looks fine in isolation hides the problems the real app would expose:
- Render it at the target device's viewport, in its conventions: for a mobile app, a phone-sized frame with its status bar, safe areas, navigation patterns, platform controls, and system fonts.
- Use the project's real design tokens, such as colours, typography, spacing, and icons, read from the codebase, and realistic data: copy and records shaped like the real ones, including long, empty, and edge-case values.
- Make every interaction that could sway the decision work, such as navigating between screens, opening sheets, switching tabs, scrolling, and entering input, using fake data in memory.

Show one variant at a time, switched from a small floating bar outside the device frame, visually distinct from the design: a left arrow, the current variant's letter and name, such as `B (Sidebar layout)`, and a right arrow, both wrapping around. The `←` and `→` keys switch too, except while an input is focused, and the URL fragment holds the current variant, so that a reload keeps it.
