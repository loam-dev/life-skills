# Git Sync Procedures

The data directory `~/.local/share/life/` is a git repository synced to a remote.

## Standard Sync

```bash
cd ~/.local/share/life
git add -A
git commit -m "update: <brief description>"
git pull --rebase
git push
```

Run sync after creating or modifying entries, or in batches after multiple changes.

## Conflict Resolution

YAML front matter conflicts are the most common type. When `git pull --rebase` reports conflicts:

1. Check which files conflict: `git diff --name-only --diff-filter=U`
2. Open each conflicted file and resolve the YAML front matter:
   - For timestamp fields (`updated_at`, `last_contact`), keep the more recent value
   - For status fields, keep the more advanced state (e.g., `done` over `in_progress`)
   - For content/body, merge both versions or keep the longer one
3. Stage resolved files: `git add <file>`
4. Continue rebase: `git rebase --continue`

If conflicts are unrecoverable:

```bash
git rebase --abort
```

Then manually inspect and retry.

## Safety

Before pulling, the sync process should back up the current state:

```bash
cp -r ~/.local/share/life ~/.local/share/life.bak.$(date +%Y%m%d-%H%M%S)
```

This allows rollback if a rebase goes wrong. Remove old backups periodically.

## Automation

To sync on a schedule, add a cron job or systemd timer that runs the standard sync commands. Ensure no concurrent writes during automated sync.
