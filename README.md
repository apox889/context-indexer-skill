# context-indexer

An Agent Skill that keeps a running, status-aware log of every brainstorming and debugging turn in a project, so long conversations do not lose the thread.

Every turn becomes a numbered block in `BRAINSTORM_LOG.md` at the project root. Each block has a title, a status, and a one-line summary of what you asked and what the agent concluded. A summary table at the top of the file gives you the whole history at a glance.

Because blocks carry a status, the agent only reasons from `Active` ideas. `Archived` and `Merged` blocks stay on the record as history but are ignored unless you explicitly point at them.

## What you get

- **Block logging** — one block per discussion turn, written automatically.
- **Sequential labels** — `[BLOCK A]`, `[BLOCK B]`, and so on, never reused.
- **Status-aware context** — `Active`, `Archived`, or `Merged`, with clear rules for what the agent is allowed to read.
- **A live index** — one table row per block at the top of the file.
- **Three plain-English commands** — archive, merge, and show index.

## Repository layout

```
.
├── README.md
└── .agents/
    └── skills/
        └── context-indexer/
            ├── SKILL.md
            └── assets/
                └── BRAINSTORM_LOG.template.md
```

- `SKILL.md` — the skill definition: YAML frontmatter plus the full workflow instructions.
- `assets/BRAINSTORM_LOG.template.md` — the starter file the skill copies into a new project.

## Install

### In a single project

Copy the skill folder into the project where you want it active:

```bash
mkdir -p .agents/skills
cp -r <repo>/.agents/skills/context-indexer .agents/skills/
```

On Windows PowerShell:

```powershell
New-Item -ItemType Directory -Force .agents\skills | Out-Null
Copy-Item -Recurse <repo>\.agents\skills\context-indexer .agents\skills\
```

### Across all your projects

Copy the same folder into your user-level skills directory instead:

```bash
mkdir -p ~/.agents/skills
cp -r <repo>/.agents/skills/context-indexer ~/.agents/skills/
```

### From GitHub

```bash
git clone https://github.com/<your-user>/<your-repo>.git
cp -r <your-repo>/.agents/skills/context-indexer .agents/skills/
```

Either way, the result should be a folder named `context-indexer` containing `SKILL.md` directly inside `.agents/skills/`. Restart or reload your agent so it picks up the new skill.

## Use

Once installed, the skill triggers on its own when you brainstorm, think out loud, debug by discussion, or ask to log something. You can also invoke it by name.

Start a session with any design conversation and the agent will create `BRAINSTORM_LOG.md` and write the first block. A log looks like this:

```markdown
# Brainstorm Log

## Index

| Block | Title | Status |
| ----- | ----- | ------ |
| A | Auth token refresh strategy | Merged |
| B | Rate limiting approach | Archived |
| C | Session storage comparison | Active |

## [BLOCK A] Auth token refresh strategy

- **Status:** Merged
- **USER:** Asked how to handle expiring access tokens.
- **AI:** Recommended short-lived tokens with a rotating refresh token.

## [BLOCK B] Rate limiting approach

- **Status:** Archived
- **USER:** Asked about per-IP vs per-user limits.
- **AI:** Suggested per-user limits with a per-IP burst allowance.

## [BLOCK C] Session storage comparison

- **Status:** Active
- **USER:** Asked whether to store sessions in Redis or cookies.
- **AI:** Recommended Redis-backed sessions, building on [BLOCK A].
```

### Commands

| You say | The agent does |
| --- | --- |
| `Archive [Block B]` | Sets block B to `Archived` and updates its index row. |
| `Merge [Block A] and [Block B]` | Marks A and B `Merged`, then creates a new `Active` block combining them. |
| `Show index` | Prints the summary table from the top of the log. |

Phrasing can be loose — "archive block b" or "merge A with B" works the same. The agent confirms if a label is ambiguous.

### Status rules in practice

- `Active` is the only status the agent reads and builds on.
- `Archived` and `Merged` blocks are history. They are ignored unless you name them.
- Merging keeps the old blocks for the record. The new block is the live one.

## Customize

Edit `SKILL.md` to tune the behaviour. The parts you are most likely to change:

- The `description` in the frontmatter — controls when the skill auto-triggers. Make it more specific if it fires too often, or add keywords if it fires too rarely.
- The block template — add fields such as `Files:` or `Decision:` if you want more structure per turn.
- The status vocabulary — if `Active` / `Archived` / `Merged` does not fit, rename them consistently in the instructions, the template, and the index table.

Keep the frontmatter `name` matching the folder name, and keep `description` a single line of prose.

## Tips

- Keep summaries to one line each. The log is an index, not a transcript.
- Archive aggressively. Fewer active blocks means sharper context.
- Merge when two threads have collapsed into one decision, rather than leaving both active.
- Commit `BRAINSTORM_LOG.md` alongside your code so the reasoning travels with the project.

## License

Add a license of your choice before publishing. MIT is a common default for skills.
