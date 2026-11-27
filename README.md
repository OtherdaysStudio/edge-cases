# edge-cases

A skill for Claude Code and other coding agents. It finds the interaction edge cases of a feature before the agent designs or builds it.

```bash
npx skills add OtherdaysStudio/edge-cases
```

## The problem

You have a composer. Escape closes it and deletes the text. That is fine.

Then you ask the agent to add file drag. The agent builds it, and it works. Two weeks later someone starts a drag, changes their mind and presses Escape. The composer closes and their text is gone. They only meant to cancel the drag.

Nothing in the brief was wrong, and nothing in the new code was wrong. The feature broke where it met the rest of the product: one input, two owners, in states that overlap.

An agent that builds from a short brief misses this class of bug almost every time, because the bug is not in the feature. This skill makes the agent look for it first.

## What the agent does

The skill runs at the idea, before the first design and before the first line of code. It has eight steps.

**1. Ledger.** Before it thinks about your feature, the agent reads the code and lists every input the product handles today: keys, hotkeys, pointer actions, voice events, system events. It includes the inputs that other parts of the app take globally. Each line has a file and a line number.

For the composer, the ledger has the line "Escape: closes the composer and drops the words." That line is the one that matters later.

**2. Things.** The agent lists every thing the feature touches that takes time or holds the person's content. For file drag: the drag, the file load, the draft text, the send.

**3. Intents.** For each thing, the agent answers seven questions from the person's side:

- They start it. From which states? What if another thing is in progress?
- They do something else while it runs.
- They cancel it midway. Which input does that?
- Something interrupts it midway.
- They do it twice.
- They change their mind after it finished.
- It fails, or it never finishes.

"They cancel the drag midway. Which input does that?" The answer is Escape.

**4. Unfinished states.** Anything that has started and not finished is a state: a held key, a drag in flight, a recording, a load, a send, an animation. The agent crosses each one with each input in the ledger.

This step is the one a plain prompt skips. A drag is not an input. It is a state, and Escape can arrive during it.

**5. Collisions.** For each answer, the agent looks up the input in the ledger. Escape already has a meaning. That is a collision, and the agent has to decide it.

**6. Hierarchy.** For each input with more than one meaning, the agent gives the order one press takes. The newest unfinished thing ends first. The most destructive meaning comes last.

```
Escape
1. A drag in flight: cancel the drag.
2. An open menu or picker: close it.
3. A file load or a waiting send: stop it.
4. The current voice take: drop it.
5. The composer: close it.
```

**7. Loss.** The agent lists every path that destroys the person's work and marks each one intended or not intended. It decides each "not intended".

**8. Bug reports.** The agent writes ten bug reports that real users send two weeks after ship, each from a concrete moment. Any case the first seven steps missed goes into the spec.

## What you receive

One section in the feature's spec, under 800 words:

- **Ledger**: the inputs that exist today.
- **Collisions**: one line each. The input, its meanings, the decision.
- **Hierarchy**: one ordered list per shared input.
- **Edge cases**: up to 20 one-line items.
- **Loss paths**: each marked intended or not intended.
- **Unproven**: each claim that needs a check in the running app.
- **Decisions for the owner**: the choices that change what the product does, each with options and a default.

Most of this is behaviour with no pixels, so it stays words. The first design then holds every state the person can see, such as a file chip or a drop target that lights up, each as a state of its component.

## Install

```bash
npx skills add OtherdaysStudio/edge-cases
```

The [skills CLI](https://github.com/vercel-labs/skills) asks which agent to install for and whether the skill is for one project or for all of them. For Claude Code, in every project, with no questions:

```bash
npx skills add OtherdaysStudio/edge-cases -g -a claude-code -y
```

The repo holds two skills, `edge-cases` and `edge-cases-backfill`. The command installs both. To take one, add `--skill edge-cases`.

## Use

Give the agent a feature idea. A vague one is fine:

> We have a composer. We need to add files to it. You can drag them in or paste them in, and you can delete them.

The skill loads when the idea adds or changes an interaction. You can also name it: "run edge-cases on this first".

## Backfill: the bugs that already shipped

`edge-cases` works on a feature you have not built yet. `/edge-cases-backfill` works on the code you have.

```
/edge-cases-backfill the composer
```

Name a surface or a folder. With no name, the agent lists the product's surfaces and takes them one at a time.

The agent reads only. It does this:

1. Finds every place in the code that claims an input: key handlers, global hotkeys and event taps, pointer and drop handlers, voice and system events.
2. Keeps each input with two or more owners, and each key that no one claims and that falls through to another app.
3. Lists everything that takes time to finish: a drag, a recording, a load, a send.
4. Crosses the two lists and reads who receives the press first. An order that a guard states is fine. An order that happens by accident is a finding.
5. Follows each cancel and discard to the work it claims to end. A discard that hides the view while the send still runs is a finding.
6. Finds every line that drops the person's content, and checks for a copy or an undo.
7. Reads each candidate again and drops the ones a guard already handles.

You receive up to 15 findings, the worst loss first. Each has the steps a person takes, what happens, what they meant, the file and line of each owner, and a proposed decision. Each is marked "confirmed by reading" or "unproven". An unproven finding depends on the operating system, so play it in the running app before anyone fixes it.

## After the build

- Each collision line is one test.
- Play each collision and each "unproven" line in the running app.
- When a bug ships anyway, add one question to step 3 of `skills/edge-cases/SKILL.md`. The skill then knows about that kind of bug for good.

## How it was tested

Four vague briefs on one macOS app. Each had a trap known in advance. Each prompt ran once per brief.

| Brief | The trap | Plain prompt | This skill |
|---|---|---|---|
| Add files to the composer | Escape during a drag | Missed | Found |
| Add undo | Cmd-Z already has two owners | Found, kept the old order | Found, decided the order |
| Return sends in voice mode | Return belongs to the front app | Found the fact, missed the harm | Found |
| Add send and discard buttons | The panel ignores the mouse | Found | Found |

The plain prompt was "write the edge cases for this feature". It finds a trap that blocks the build. It misses a trap that shows only in use.

## Limits

- One run per prompt per brief. The numbers are small.
- The skill reads code. It cannot see what the operating system does with a key or a drag, which is why the output has an "unproven" list.
- On one brief the plain prompt found an issue that the skill missed. For a feature that touches shared inputs, run the skill twice and merge the results.
