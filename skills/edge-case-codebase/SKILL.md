---
name: edge-case-codebase
description: >-
  Use when asked to audit a whole codebase for edge cases, or to find
  shipped bugs of the kind "one input, two meanings" (Escape closes the
  wrong thing, a discard that still sends, a key that leaks to another app)
  and paths that lose the person's work. Maps the code into components,
  checks where they collide, and writes a report with a screenshot of the
  exact screen and control for each finding. For a new feature use
  edge-cases.
---

# Edge case codebase

The `edge-cases` skill stops collisions before a feature is built. This one
finds the collisions that already shipped, across the whole codebase, and
shows each one on a screenshot so a person sees at once which screen and
which button it is about.

Read the code only. Write one report file and its screenshots. Fix nothing
unless the person asks.

## 1. Map the components

Read the whole codebase and split it into components. A component is a thing
the person meets (a screen, a panel, a composer, a menu, a toast) or a thing
that claims an input for the whole app (a hotkey, an event tap, a drop
catcher). Use the project's own index or module list when it has one.

For each component, write one row:

- its name, in the words the product uses;
- its purpose, in one sentence from the person's side;
- its files;
- the screen it shows on;
- the content of the person's that it holds (a draft, a recording, a
  selection, files), or "none".

Write the map into the report first. It is the first thing the person reads,
and every later step uses its names.

## 2. Collect the claims, cheaply

For each component, list three things with file:line. Nothing else.

- **Claims**: each input it takes. Keys, key equivalents, shortcuts, clicks,
  drags, drops, hover, scroll, gestures, voice events, system events (sleep,
  focus lost, a setting changed, quit). Search the whole product for claims
  that reach every window: global hotkeys, event taps, key monitors. They
  cause the most findings and they sit far from the screen they break.
- **Unfinished states**: each thing it starts that takes time to finish: a
  held key, a drag in flight, a recording, a load, a send, an animation, an
  open menu. Each wait between a stop and its delivery counts too (an
  `await`, a callback, a timer).
- **Loss lines**: each line that clears, drops or replaces the person's
  content, and each entry point from outside that writes over it.

This pass is wide and shallow. With more than about eight components, give
one subagent each group of components, on the cheapest model that reads code
well, and ask for these three lists only. State the agent count and the cost
to the person before you start them.

## 3. Narrow

Merge the lists. Deep reading costs the most, so do it only here. Keep:

- each input that two or more components claim;
- each key a component shows on screen but nobody claims (it falls through
  to a default, or to another app);
- each stop, discard and cancel;
- each loss line.

Cross each kept input with each unfinished state that can be live when the
press arrives. Each pair is one check. Drop a pair when the two can never be
live at the same time, and say why in one line.

## 4. Check each pair

Read the code path of both owners.

- **Order.** Who receives the press first? Is that order written as a guard,
  or does it happen by accident (registration order, which window is key, a
  tap that runs before the view)? An accident is a finding.
- **Cancel.** Follow each stop, discard and cancel to the work it claims to
  end. Does the work check for the cancel before it delivers? Read what the
  lower layer's cancel really clears. A discard that hides the view while
  the send still runs is a finding.
- **Loss.** For each loss line: which input reaches it, from which states,
  and is there a copy, a history row or an undo?

## 5. Prove each finding

Read the whole path again. Drop a finding when a guard handles it. Mark what
survives:

- **Confirmed by reading**: both paths cited, no guard found.
- **Unproven**: it depends on what the operating system does, so it needs a
  live check.

Never report from a function name or a guess.

## 6. Take the screenshots

Each finding has one screenshot of the exact screen, in the state where the
problem starts.

- Run the product and capture the real screen, with the tool the project
  already uses to run and capture (a browser tool, a simulator, a screen
  capture command). Put the product into the state first: the draft typed,
  the menu open, the drag started.
- No way to run it: use the approved design of that screen, or a screenshot
  the repo already holds. Say which one you used.
- Mark the control: one outline around it and one short label. Mark a second
  control only when the finding is about two.
- Never draw a mock-up, and never use a screenshot of a different state.
- No screenshot possible: write "No screenshot" and give the place in words:
  screen, then region, then the control's own label.

Name each file after its finding: `03-composer-discard-still-sends.png`.

## 7. Write the report

One file: `docs/superpowers/specs/YYYY-MM-DD-edge-cases-codebase.md`, with
the screenshots in a folder of the same name beside it. A repo with no
`docs/superpowers` folder: `docs/edge-cases/`. The file is a spec, so a
planning step can turn it into fixes.

Write it for a person who has not read the code.

- **Summary**: how many findings, how many confirmed, and the three worst in
  one line each.
- **Components**: the map from step 1, as a table.
- **Findings**, max 15, in this order: lost work, then a wrong action that
  cannot be undone, then a key or shortcut that stops working. Each one:
  - a heading in plain words that names the screen and the control:
    "Composer, Discard button: the message still sends";
  - the screenshot;
  - **Steps**: what the person does, as a numbered list;
  - **What happens** and **what they meant**, one sentence each;
  - **Where**: file:line for each owner;
  - **Status**: confirmed by reading, or unproven;
  - **Decision**: the order or the fix you propose, in one line.
- **Inputs with no stated order**: each shared input and its owners.
- **Not read**: the components you did not cover, and why.

Then tell the person the file path, the count, and the three worst findings.
Open the file for them if you can.

## After

- Each confirmed finding is one failing test before its fix.
- Play each unproven finding in the running product before anyone fixes it.
