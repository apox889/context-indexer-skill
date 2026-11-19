---
name: context-indexer
description: Maintains a BRAINSTORM_LOG.md file at the project root that records every brainstorming, debugging, or idea-discussion turn as a numbered, titled block with a status. Use this skill whenever the user is brainstorming, thinking out loud, debugging by discussion, exploring options, or explicitly asks to log, archive, merge, or show the index of discussion blocks. Do not use it for routine code edits or file operations that involve no discussion.
---

# Context Indexer

This skill keeps a durable, skim-friendly record of our thinking so that long projects do not lose earlier decisions. Every discussion turn becomes a numbered block in `BRAINSTORM_LOG.md`, and every block carries a status that tells you whether it is still usable context.

## Core rule: log every turn

Whenever we brainstorm, debug, or discuss ideas, append a new block to `BRAINSTORM_LOG.md` in the current project root. A "turn" means one of my prompts plus your response.

- If the file does not exist, create it using the template in `assets/BRAINSTORM_LOG.template.md`.
- Never delete or rewrite old blocks. Blocks are append-only history; only their status line changes.
- Write the block at the end of the turn, after you have finished your answer, so the summary describes what you actually concluded.
- Keep summaries short. One or two sentences each. This file is an index, not a transcript.

## Block structure

Label turns sequentially with block letters: `[BLOCK A]`, `[BLOCK B]`, `[BLOCK C]`, and so on. Continue from the highest existing letter; never reuse a label, even if a block was archived or merged.

Each block must contain exactly these parts:

```
## [BLOCK C] Short descriptive title

- **Status:** Active
- **USER:** One-line summary of my prompt.
- **AI:** One-line summary of your response and conclusion.
```

Rules for the parts:

- **Title** — a few words naming the topic, not the question. Prefer "Auth token refresh strategy" over "User asked about tokens".
- **Status** — exactly one of `Active`, `Archived`, or `Merged`.
- **USER** — what I was after, in my terms.
- **AI** — the recommendation, decision, or finding, in your terms.

Do not put code, logs, or long explanations in a block. Point at the relevant file or conversation instead if detail matters.

## Summary index at the top

At the very top of `BRAINSTORM_LOG.md`, above every block, maintain a one-line-per-block index in a table:

```
| Block | Title | Status |
| ----- | ----- | ------ |
| A | Auth token refresh strategy | Merged |
| B | Rate limiting approach | Archived |
| C | Session storage comparison | Active |
```

Update the index in the same edit as the block, so the table always matches the blocks below it. New rows go in label order.

## Status rules

`Active` blocks are the working context. `Archived` and `Merged` blocks are history.

- Only read and build upon `Active` blocks. Treat them as the current state of our thinking.
- Strictly ignore `Archived` and `Merged` blocks. Do not cite them, do not resurrect their decisions, and do not let them influence a recommendation.
- The only exception is when I explicitly reference an archived or merged block by label. In that case, read it, use it for that turn, but do not change its status and do not treat it as active context afterwards.
- When a new block depends on an earlier one, reference it inline, e.g. "builds on [BLOCK C]".

## Commands

I will give these as plain English. Recognize them even when phrased loosely, and confirm the interpretation if the block label is ambiguous.

### "Archive [Block X]"

Change that block's `Status:` to `Archived` and update its row in the summary index.

- Archive only the block named. Do not archive neighbours, and do not renumber anything.
- If X is already `Archived` or `Merged`, say so and make no change.

### "Merge [Block X] and [Block Y]"

Combine the ideas of the named blocks into one new block.

1. Change the status of X and Y to `Merged`.
2. Create a new block with the next unused letter, status `Active`, and a title that describes the combined topic.
3. In the new block's `USER` line, say which blocks were merged. In the `AI` line, state the combined conclusion, dropping anything that no longer applies.
4. Update the index: X and Y now show `Merged`, and the new block is added as `Active`.
5. Leave the original block bodies untouched apart from their status line, so the history stays readable.

Merging more than two blocks works the same way: mark them all `Merged`, then create one new `Active` block.

### "Show index"

Display the summary table from the top of `BRAINSTORM_LOG.md` as a table in your reply. Do not dump the block bodies unless I ask for a specific block.

## Editing discipline

- Make the smallest edit that satisfies the request: one new block plus one new index row, or one status line plus one index cell.
- Keep the file valid Markdown with a single `#` heading at the top and `##` headings for blocks.
- If `BRAINSTORM_LOG.md` is missing when I issue a command, say so and offer to create it from the template rather than inventing history.
- If the file looks corrupted or has out-of-order labels, repair the index and report what you changed instead of silently rewriting blocks.
