---
name: life-skills
description: Personal knowledge management system for memories, people, projects, engagements, and thoughts. Use when the user wants to remember something, look up a person/project, track tasks, journal, search their personal knowledge base, or sync their life data. Activates on phrases like "remember this", "who is", "my projects", "what am I working on", "journal", "show my dashboard", or any personal knowledge management request.
---

# Life Skills

Personal knowledge management using flat markdown files with YAML front matter stored in a local git repository.

## Data Location

All data lives in `~/.local/share/life/` with one subdirectory per domain:

```
~/.local/share/life/
  memories/
  people/
  projects/
  engagements/
  thoughts/
```

Each entry is a single markdown file with YAML front matter and an optional body.

## Identity and Secrets

Read `~/.repos.json` for repository configuration. Use `pass` for secrets:

```bash
pass show gh/<org>          # GitHub token
pass show email/<account>   # Email credentials
```

To get the current user's git identity:

```bash
git config --global user.name
git config --global user.email
```

## File Naming Conventions

| Domain       | Pattern                            | Example                              |
| ------------ | ---------------------------------- | ------------------------------------ |
| memories     | `YYYY-MM-DD-slugified-title.md`    | `2025-06-15-prefers-dark-mode.md`    |
| people       | `slugified-full-name.md`           | `jane-doe.md`                        |
| projects     | `slugified-project-name.md`        | `life-skills.md`                     |
| engagements  | `YYYY-MM-DD-slugified-title.md`    | `2025-06-15-review-api-design.md`    |
| thoughts     | `YYYY-MM-DD-HHMMSS.md`            | `2025-06-15-143022.md`               |

Slugify: lowercase, replace non-alphanumeric with hyphens, collapse multiple hyphens, trim.

## Domain: Memories

Store facts, preferences, decisions, learnings, and context snippets.

### Create

Write a new file in `~/.local/share/life/memories/`:

```yaml
---
title: Prefers dark mode
category: preference
source: manual
confidence: 1.0
tags: [ui, settings]
project_id: life-skills
person_id: jane-doe
created_at: 2025-06-15T10:30:00Z
---
User mentioned they always use dark mode across all editors and terminals.
```

Required fields: `title`, body content. Optional: `category` (fact|preference|decision|learning|context), `source`, `confidence` (0-1), `tags`, `project_id`, `person_id`.

### Search

Use Grep to search memory content:

```
Grep pattern="dark mode" path="~/.local/share/life/memories/"
```

Filter by category by grepping front matter:

```
Grep pattern="^category: preference" path="~/.local/share/life/memories/"
```

### Update

Read the file, modify front matter or body, write it back.

## Domain: People

Track contacts with relationship context.

### Create

Write a new file in `~/.local/share/life/people/`:

```yaml
---
name: Jane Doe
email: jane@example.com
phone: "+1-555-0100"
company: Acme Corp
role: Engineering Manager
relationship: colleague
tags: [engineering, frontend]
last_contact: 2025-06-15
created_at: 2025-06-15T10:30:00Z
---
Met at the 2025 engineering offsite. Leads the frontend platform team.
Interested in design systems and accessibility.
```

Required: `name`. Optional: `email`, `phone`, `company`, `role`, `relationship`, `context` (body), `tags`, `last_contact`.

### Search

```
Grep pattern="company: Acme" path="~/.local/share/life/people/"
```

List all people:

```
Glob pattern="*.md" path="~/.local/share/life/people/"
```

### Get Related Data

When retrieving a person, also search memories and engagements that reference their slug:

```
Grep pattern="person_id: jane-doe" path="~/.local/share/life/memories/"
Grep pattern="jane-doe" path="~/.local/share/life/engagements/"
```

## Domain: Projects

Track projects with status, priority, and repository links.

### Create

Write a new file in `~/.local/share/life/projects/`:

```yaml
---
name: Life Skills
description: Personal knowledge management skill
repo_url: https://github.com/gh/<org>/life-skills
local_path: ~/life-skills
status: active
priority: 8
tech_stack: [typescript, markdown]
tags: [tools, productivity]
identity_id: personal
created_at: 2025-06-15T10:30:00Z
---
Flat-file personal knowledge management system that replaces the old SQLite-backed life-os.
```

Required: `name`. Optional: `description`, `repo_url`, `local_path`, `status` (active|paused|archived|idea), `priority` (higher = more important), `tech_stack`, `tags`, `identity_id`.

### List by Status

```
Grep pattern="^status: active" path="~/.local/share/life/projects/"
```

### Get Full Project View

When retrieving a project, also fetch related memories, engagements, and people:

```
Grep pattern="project_id: life-skills" path="~/.local/share/life/memories/"
Grep pattern="project_id: life-skills" path="~/.local/share/life/engagements/"
```

## Domain: Engagements

Track tasks, decisions, meetings, and milestones linked to projects and people.

### Create

Write a new file in `~/.local/share/life/engagements/`:

```yaml
---
title: Review API design
type: task
project_id: life-skills
status: open
priority: 7
due_date: 2025-06-20
tags: [api, review]
people:
  - jane-doe
  - bob-smith
created_at: 2025-06-15T10:30:00Z
---
Review the new REST API design doc and provide feedback on authentication flow.
```

Required: `project_id`, `type`, `title`. Optional: `description` (body), `status` (open|in_progress|done|cancelled), `priority`, `due_date`, `tags`, `people` (list of person slugs).

### Complete

Read the file, set `status: done`, add `completed_at` timestamp, write it back.

### List Open Engagements

```
Grep pattern="^status: open" path="~/.local/share/life/engagements/"
```

### Filter by Due Date

```
Grep pattern="^due_date: 2025-06" path="~/.local/share/life/engagements/"
```

## Domain: Thoughts

Free-form journal entries.

### Create

Write a new file in `~/.local/share/life/thoughts/`:

```yaml
---
mood: focused
tags: [architecture, planning]
created_at: 2025-06-15T14:30:22Z
---
Thinking about migrating to flat files. The SQLite approach works but
creates friction with git sync. Markdown files are more transparent,
easier to diff, and work better with standard tools.
```

Required: body content. Optional: `mood`, `tags`.

### List Recent

```
Glob pattern="*.md" path="~/.local/share/life/thoughts/"
```

Files sort chronologically by filename.

### Search by Mood

```
Grep pattern="^mood: focused" path="~/.local/share/life/thoughts/"
```

## Sync

The data directory is a git repository. Sync with:

```bash
cd ~/.local/share/life && git add -A && git commit -m "update" && git pull --rebase && git push
```

Always sync after creating or modifying entries. See [reference/sync.md](reference/sync.md) for conflict resolution and detailed procedures.

## Dashboard

When the user asks for a dashboard or overview, build it by aggregating:

1. **Active projects**: `Grep pattern="^status: active" path="~/.local/share/life/projects/"`
2. **Open engagements**: `Grep pattern="^status: open" path="~/.local/share/life/engagements/"` -- also check `in_progress`
3. **Upcoming due dates**: `Grep pattern="^due_date:" path="~/.local/share/life/engagements/"` -- filter for near-future dates
4. **Recent memories**: List the 10 most recently modified files in `memories/`
5. **Recent thoughts**: List the 5 most recent files in `thoughts/`

Present as a structured summary with sections for each domain.

## Behavioral Guidelines

### When to Proactively Remember

- The user shares a preference, opinion, or decision -- create a memory with category `preference` or `decision`
- The user mentions a person with details -- create or update a person entry
- A project milestone is reached -- create a memory with category `fact` and link the project
- The user learns something new -- create a memory with category `learning`

### When to Search

- Before answering questions about people, projects, or past decisions -- search first
- When the user says "remember when" or "what did I say about" -- search memories
- When starting work on a project -- load the project file and recent related engagements
- When the user mentions a name -- check if the person exists in people/

### General Behavior

- Always confirm before creating entries (unless the user explicitly says "remember this")
- When creating entries, show the user what was written
- Keep front matter fields consistent with the schemas defined in [reference/formats.md](reference/formats.md)
- Use ISO 8601 format for all dates and timestamps
- Sync after batches of changes, not after every single write

## Reference Files

- [reference/formats.md](reference/formats.md) -- Detailed YAML front matter schemas and examples for each domain
- [reference/migration.md](reference/migration.md) -- Guide for migrating data from the old life-os SQLite database
- [reference/sync.md](reference/sync.md) -- Git sync procedures and conflict resolution
