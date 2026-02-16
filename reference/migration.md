# Migration from life-os SQLite

Guide for exporting data from the old life-os SQLite database (`~/.local/share/life/life.db`) to flat markdown files.

## Prerequisites

- Access to the SQLite database at `~/.local/share/life/life.db`
- Target directories already created under `~/.local/share/life/`

## Export Memories

```sql
SELECT id, title, content, category, source, confidence,
       project_id, person_id, tags, created_at, updated_at
FROM memories ORDER BY created_at;
```

For each row, create `memories/YYYY-MM-DD-slugified-title.md`:

```yaml
---
title: <title>
category: <category>
source: <source>
confidence: <confidence>
tags: <parse JSON array>
project_id: <project_id>
person_id: <person_id>
created_at: <created_at>
updated_at: <updated_at>
---
<content>
```

Omit null/empty fields from front matter.

## Export People

```sql
SELECT id, name, email, phone, company, role, relationship,
       context, tags, created_at, updated_at
FROM people ORDER BY name;
```

For each row, create `people/<id>.md` (the `id` column is already a slug):

```yaml
---
name: <name>
email: <email>
phone: <phone>
company: <company>
role: <role>
relationship: <relationship>
tags: <parse JSON array>
created_at: <created_at>
updated_at: <updated_at>
---
<context>
```

## Export Projects

```sql
SELECT id, name, description, repo_url, local_path, status,
       priority, tech_stack, tags, identity_id, created_at, updated_at
FROM projects ORDER BY priority DESC;
```

For each row, create `projects/<id>.md`:

```yaml
---
name: <name>
description: <description>
repo_url: <repo_url>
local_path: <local_path>
status: <status>
priority: <priority>
tech_stack: <parse JSON array>
tags: <parse JSON array>
identity_id: <identity_id>
created_at: <created_at>
updated_at: <updated_at>
---
```

## Export Engagements

```sql
SELECT id, project_id, type, title, description, status,
       priority, due_date, tags, people, created_at, updated_at
FROM engagements ORDER BY created_at;
```

For each row, create `engagements/YYYY-MM-DD-slugified-title.md` (date from `created_at`):

```yaml
---
title: <title>
type: <type>
project_id: <project_id>
status: <status>
priority: <priority>
due_date: <due_date>
tags: <parse JSON array>
people: <parse JSON array>
created_at: <created_at>
updated_at: <updated_at>
---
<description>
```

## Export Thoughts

```sql
SELECT id, content, mood, tags, created_at
FROM thoughts ORDER BY created_at;
```

For each row, create `thoughts/YYYY-MM-DD-HHMMSS.md` (from `created_at`):

```yaml
---
mood: <mood>
tags: <parse JSON array>
created_at: <created_at>
---
<content>
```

## Post-Migration

1. Verify file counts match row counts for each table
2. Spot-check a few entries per domain for correct formatting
3. Initialize git: `cd ~/.local/share/life && git init && git add -A && git commit -m "initial migration from life-os"`
4. Add remote and push: `git remote add origin <repo-url> && git push -u origin main`
5. Back up the original `life.db` before removing the old life-os installation
