---
name: edge-cases
description: >-
  Use before you design or build any feature that adds or changes an
  interaction: a new input, gesture, button, mode, drop target, shortcut or
  anything that takes time. Finds the collisions (one input, two meanings)
  and the paths that lose the person's work, and decides them, before the
  first frame or the first line of code. Works from a vague brief.
---

# Edge cases

A feature works alone and breaks where it meets the rest of the product. The
usual cause: one input (Escape, Return, Cmd-Z, Backspace, a hotkey, a click)
has two owners in states that overlap. Find those before you make anything.

Run this at the idea, before the first design and before the first line of
code. Never add it in a later round.

## The method, in this order

1. **Ledger.** Before you think about the new feature, read the code and list
   every input the surface handles today and what each one does: every key,
   every hotkey, every pointer action, every voice event, every system event.
   Include inputs that other parts of the app take globally (event taps,
   hotkeys, drop catchers). Cite file:line. No code yet: build the ledger
   from the designs and their component descriptions.
2. **Things.** List every thing the feature touches that takes time or holds
   the person's content: each new action in the brief and each existing one.
3. **Intents.** For each thing, answer from the person's side:
   - (a) they start it: from which states, and what if another thing is in progress;
   - (b) they do something else while it runs;
   - (c) they cancel it midway, and which input does that;
   - (d) something interrupts it midway (the system, another feature, a hotkey);
   - (e) they do it twice;
   - (f) they change their mind after it finished;
   - (g) it fails or never finishes.
4. **Unfinished states.** Anything that has started and not finished is a
   state: a held key, a pointer gesture in flight, a recording, a load, a
   send, an animation. Cross each one with each input in the ledger.
5. **Collisions.** For each answer, name the input that does it and look it up
   in the ledger. If the input already has a meaning, list the collision and
   decide it.
6. **Hierarchy.** For each input with more than one meaning, give the order
   one press takes: first, second, last. The newest unfinished thing ends
   first. The most destructive meaning comes last.
7. **Loss.** List every path that destroys the person's work. Mark each
   intended or not intended. Decide each "not intended".
8. **Bug reports.** Write 10 bug reports real users send two weeks after
   ship, each from a concrete moment. Add any case the steps above missed.

## Output

Write it into the feature's spec. Under 800 words.

- **Ledger**: max 12 lines.
- **Collisions**: one line each: the input, its meanings, the decision.
- **Hierarchy**: one ordered list per shared input.
- **Edge cases**: max 20 one-line items.
- **Loss paths**: each marked intended or not intended.
- **Unproven**: each claim that needs a live check in the running app.
- **Decisions for the owner**: each decision that changes what the product
  does, with options and a default.

## What becomes a design and what stays words

Most of this is behaviour with no pixels. It stays in the spec: orders,
collisions, loss decisions.

The first design already holds every state the person can see (a file chip, a
drop target that lights up, a refused drop). Draw each as a state of its
component, not as an extra screen.

## After the build

- Each collision line is one test.
- Play each collision and each "unproven" line in the running app.
- A bug that ships anyway adds one question to step 3 of this file.
