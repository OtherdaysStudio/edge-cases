# edge-cases

Two skills that make a coding agent find interaction bugs it otherwise ships.

```bash
npx skills add OtherdaysStudio/edge-cases
```

## The bug

Escape closes your composer and deletes the text. Then you add file drag. Someone starts a drag, changes their mind and presses Escape. The composer closes and their text is gone.

The new feature was fine. It broke where it met the rest of the product: one input, two meanings. An agent that builds from a short brief does not look there.

## edge-cases

For a feature you have not built yet. Give the agent the idea, however vague. Before it designs or writes code, it:

1. Lists every input the product handles today.
2. Lists everything that takes time: a drag, a recording, a send.
3. Asks of each one: what if the person starts it, does something else, cancels it, does it twice, or it fails?
4. Decides each input that has two meanings, and gives it an order.
5. Lists every path that loses the person's work.

For the composer, the result is an order for Escape: the drag first, the composer last.

## edge-case-codebase

For the code you already have.

```
/edge-case-codebase
```

The agent maps the code into components, finds where two of them take the same input, and reads who wins. It writes one report. Each finding has a screenshot of the exact screen with the control marked, the steps to cause it, and the file and line.

It reads only. It fixes nothing.

## Tested

Four vague briefs on one macOS app, each with a trap known in advance, one run each. A plain "write the edge cases" prompt found 2 of 4. `edge-cases` found 4 of 4.

The skills read code. Play each finding in the running product before you trust it.
