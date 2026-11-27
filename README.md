# edge-cases

A Claude Code skill. It finds interaction edge cases before a feature is designed or built.

A feature works alone and breaks where it meets the rest of the product. The usual cause is one input with two owners. Example: Escape closes a composer and deletes its text. You add file drag. Now Escape pressed during a drag closes the composer, when the person only meant to cancel the drag.

The skill makes the agent list every input the product handles today, cross it with every unfinished state, decide each collision, and give each shared input an order.

## Install

```bash
git clone https://github.com/OtherdaysStudio/edge-cases ~/.claude/skills/edge-cases
```

## Use

Give Claude a feature idea, however vague. The skill runs before the first design or the first line of code. You can also name it: "run edge-cases on this".

## How it was tested

Four vague briefs on one macOS app, each with a trap known in advance, one run per prompt. This skill found 4 of 4. A plain "write the edge cases" prompt found 2 of 4. The plain prompt finds a trap that blocks the build. It misses a trap that shows only in use.

All findings come from reading code. Play each one in the running app.
