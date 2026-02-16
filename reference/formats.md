# Data Formats Reference

Detailed YAML front matter schemas for each domain. Every file is a markdown document with YAML front matter delimited by `---`.

## Memories

```yaml
---
title: string           # required -- short descriptive title
content: string         # alternative to body; prefer using the markdown body instead
category: string        # fact | preference | decision | learning | context
source: string          # where the memory came from (default: "manual")
confidence: number      # 0.0 to 1.0 (default: 1.0)
tags: [string]          # list of tags for categorization
project_id: string      # slug of a linked project (must match a filename in projects/)
person_id: string       # slug of a linked person (must match a filename in people/)
created_at: string      # ISO 8601 timestamp
---
Body text with additional detail about the memory.
```

### Category Descriptions

| Category     | Use for                                                     |
| ------------ | ----------------------------------------------------------- |
| `fact`       | Objective information: dates, numbers, names, outcomes      |
| `preference` | User likes, dislikes, tool choices, style preferences       |
| `decision`   | Choices made and their rationale                            |
| `learning`   | Insights, lessons, things discovered                        |
| `context`    | Background information that helps understand a situation    |

### Example

```yaml
---
title: Prefers TypeScript over JavaScript
category: preference
source: conversation
confidence: 0.9
tags: [languages, tooling]
created_at: 2025-06-15T10:30:00Z
---
User consistently chooses TypeScript for new projects. Mentioned that type safety
catches bugs early and improves IDE support.
```

## People

```yaml
---
name: string            # required -- full name
email: string           # email address
phone: string           # phone number (use quotes for + prefix)
company: string         # company or organization
role: string            # job title or role
relationship: string    # e.g. colleague, client, friend, mentor, manager
tags: [string]          # categorization tags
last_contact: string    # YYYY-MM-DD date of last interaction
created_at: string      # ISO 8601 timestamp
updated_at: string      # ISO 8601 timestamp
---
Free-form notes about the person: how you met, shared interests, context.
```

### Example

```yaml
---
name: Bob Smith
email: bob@example.com
company: Acme Corp
role: Senior Engineer
relationship: colleague
tags: [engineering, backend, rust]
last_contact: 2025-06-10
created_at: 2025-05-01T09:00:00Z
---
Works on the payments service. Met during the API redesign project.
Strong opinions on error handling patterns. Mentors junior engineers.
```

## Projects

```yaml
---
name: string            # required -- project name
description: string     # brief project description
repo_url: string        # git repository URL
local_path: string      # local filesystem path to the repo
status: string          # active | paused | archived | idea
priority: number        # higher = more important (no fixed range, relative)
tech_stack: [string]    # technologies used
tags: [string]          # categorization tags
identity_id: string     # identity slug for git/GitHub operations
created_at: string      # ISO 8601 timestamp
updated_at: string      # ISO 8601 timestamp
---
Extended description, goals, notes about the project.
```

### Status Descriptions

| Status     | Meaning                                           |
| ---------- | ------------------------------------------------- |
| `active`   | Currently being worked on                         |
| `paused`   | Temporarily on hold, intend to resume             |
| `archived` | Completed or abandoned, kept for reference        |
| `idea`     | Not yet started, captured for future consideration|

### Example

```yaml
---
name: Life Skills
description: Flat-file personal knowledge management
repo_url: https://github.com/<org>/life-skills
local_path: ~/repositories/life-skills
status: active
priority: 8
tech_stack: [markdown, git]
tags: [tools, productivity, personal]
identity_id: personal
created_at: 2025-06-01T08:00:00Z
---
Replaces the old SQLite-backed life-os with flat markdown files.
Easier to sync, diff, and inspect. Uses YAML front matter for structured data.
```

## Engagements

```yaml
---
title: string           # required -- engagement title
type: string            # required -- task | decision | meeting | milestone
project_id: string      # required -- slug of the parent project
description: string     # alternative to body; prefer using the markdown body
status: string          # open | in_progress | done | cancelled (default: open)
priority: number        # higher = more important
due_date: string        # YYYY-MM-DD
tags: [string]          # categorization tags
people: [string]        # list of person slugs involved
created_at: string      # ISO 8601 timestamp
updated_at: string      # ISO 8601 timestamp
completed_at: string    # ISO 8601 timestamp, set when status becomes "done"
---
Detailed description of the engagement, notes, action items.
```

### Type Descriptions

| Type        | Use for                                                |
| ----------- | ------------------------------------------------------ |
| `task`      | Actionable work items with a clear definition of done  |
| `decision`  | Choices that need to be made or were made              |
| `meeting`   | Scheduled meetings, 1:1s, syncs                        |
| `milestone` | Key checkpoints or deliverables                        |

### Status Flow

```
open -> in_progress -> done
  \                 \-> cancelled
   \-> cancelled
```

### Example

```yaml
---
title: Review API authentication design
type: task
project_id: life-skills
status: in_progress
priority: 7
due_date: 2025-06-20
tags: [api, security, review]
people:
  - jane-doe
  - bob-smith
created_at: 2025-06-15T10:30:00Z
---
Review the proposed OAuth2 flow for the new API. Focus on:
- Token refresh strategy
- Scope definitions
- Rate limiting per client
```

## Thoughts

```yaml
---
mood: string            # optional -- e.g. focused, frustrated, excited, calm, anxious
tags: [string]          # optional categorization tags
created_at: string      # ISO 8601 timestamp
---
Free-form journal entry. No required structure. Write whatever is on your mind.
Can be multi-paragraph, include lists, code snippets, or anything else.
```

### Example

```yaml
---
mood: excited
tags: [architecture, migration]
created_at: 2025-06-15T14:30:22Z
---
Just realized that flat markdown files solve the sync problem entirely.
No more WAL checkpoints, no more binary diffs. Every change is a clean
text diff that git handles natively.

Next steps:
- Write migration script from SQLite
- Define YAML schemas
- Test conflict resolution with concurrent edits
```
