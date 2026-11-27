---
name: edge-cases-backfill
description: >-
  Use when asked to backfill edge cases, audit an existing codebase for
  input collisions, or find shipped bugs of the kind "one input, two
  meanings" (Escape closes the wrong thing, a discard that still sends, a
  key that leaks to another app) and paths that lose the person's work.
  Reads code that is already built. For a new feature use edge-cases.
---

# Edge cases backfill

The `edge-cases` skill stops collisions before a feature is built. This one
finds the collisions that already shipped.

Read only. Report what you find. Fix nothing unless the person asks.

## Scope

The argument names a surface ("the composer") or a folder. With no argument,
list the product's surfaces first and take them one at a time, the ones that
hold the person's content first. Never mix two surfaces in one ledger.

## The method, in this order

1. **Find every claim.** Search the code for each place that takes an input,
   and list it with file:line and the state it applies in:
   - keys: key-down handlers, key equivalents, menu shortcuts, responder
     overrides, `keydown` listeners;
   - global claims: event taps, registered hotkeys, monitors, shortcuts that
     work while another app is in front;
   - the pointer: clicks, drags, drops, hover, scroll, gesture recognisers,
     anything that turns pointer events on or off;
   - voice and system events: start, stop, silence, sleep, focus lost,
     a setting changed, quit.
2. **Group by input.** Sort the claims by input. Keep each input that has two
   or more owners, and each key the surface shows but does not claim (it
   falls through to a default or to another app).
3. **List the unfinished states.** Everything on the surface that starts and
   takes time to finish: a held key, a drag in flight, a recording, a load, a
   send, an animation, an open menu.
4. **Cross them.** For each kept input and each unfinished state, read the
   code path and answer: who receives the press first? Is that order written
   in code as a guard, or does it happen by accident (registration order,
   which window is key, a tap that runs before the view)?
5. **Check each cancel.** For each stop, discard and cancel path, follow the
   work it claims to end. Does the work check for the cancel before it
   delivers? A discard that hides the view while the send still runs is a
   finding.
6. **Audit loss.** Find every line that clears or drops the person's content.
   For each: which input reaches it, from which states, and is there a copy,
   a history row or an undo?
7. **Prove each candidate.** Read the whole path again, both owners. Drop a
   candidate when a guard handles it. Mark what survives:
   - **Confirmed by reading**: both paths cited, no guard found;
   - **Unproven**: depends on what the operating system does, so it needs a
     live check.
   Never report from a function name or a guess.

## Output

Findings, the worst loss first, max 15. One block each:

- **The moment**: the steps a person takes, as a numbered list.
- **What happens** and **what they meant**.
- **Where**: file:line for each owner.
- **Status**: confirmed by reading, or unproven.
- **Decision**: the order or the fix you propose, in one line.

Then:

- **Inputs with no stated order**: each shared input and its owners.
- **Not read**: the surfaces and folders you did not cover.

Under 900 words. If you find nothing, say so and list what you read.

## After

- Each confirmed finding is one failing test before its fix.
- Play each unproven finding in the running app before anyone fixes it.
