# life-skills

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill for personal knowledge management. Store and search memories, people, projects, engagements, and journal entries as flat markdown files.

## Install

```bash
claude skill install loam-dev/life-skills
```

## Setup

Create the data directory:

```bash
mkdir -p ~/.local/share/life/{memories,people,projects,engagements,thoughts}
cd ~/.local/share/life && git init
```

Optionally add a remote for multi-machine sync:

```bash
cd ~/.local/share/life
git remote add origin <your-private-repo-url>
git push -u origin main
```

## Usage

Just talk to Claude naturally. The skill activates on personal knowledge management requests:

| You say | What happens |
|---------|-------------|
| "Remember that I prefer dark mode" | Creates a memory file with category `preference` |
| "Who is Jane Doe?" | Searches `people/` and shows related memories/engagements |
| "Show my dashboard" | Aggregates active projects, open tasks, recent memories |
| "What am I working on?" | Lists active projects and in-progress engagements |
| "Journal: thinking about the migration" | Creates a timestamped thought entry |
| "Add a task to review the API design" | Creates an engagement linked to a project |

## How it works

All data lives in `~/.local/share/life/` as markdown files with YAML front matter:

```
~/.local/share/life/
  memories/       facts, preferences, decisions, learnings
  people/         contacts with relationship context
  projects/       project tracking with status and priority
  engagements/    tasks, meetings, decisions, milestones
  thoughts/       free-form journal entries
```

Claude uses its built-in tools (Read, Write, Grep, Glob) to manage these files directly. No database, no server process, no dependencies.

### Sync

The data directory is a git repo. Claude syncs with:

```bash
cd ~/.local/share/life && git add -A && git commit -m "update: <brief description>" && git pull --rebase && git push
```

YAML front matter files merge cleanly in git.

## Domains

| Domain | File pattern | Key fields |
|--------|-------------|------------|
| Memories | `YYYY-MM-DD-slug.md` | title, category (fact\|preference\|decision\|learning\|context) |
| People | `slug.md` | name, company, role, relationship |
| Projects | `slug.md` | name, status (active\|paused\|archived\|idea), priority |
| Engagements | `YYYY-MM-DD-slug.md` | title, type (task\|decision\|meeting\|milestone), status, due_date |
| Thoughts | `YYYY-MM-DD-HHMMSS.md` | mood, tags |

## Reference

- [`reference/formats.md`](reference/formats.md) -- YAML front matter schemas and examples for each domain
- [`reference/sync.md`](reference/sync.md) -- Git sync procedures and conflict resolution
- [`reference/migration.md`](reference/migration.md) -- Migrating from the old life-os SQLite database

## License

MIT
