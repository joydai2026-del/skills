# Troubleshooting Reference

Fix commands and resolution patterns for every health check status.

## WARN/FAIL Fix Table

| Status | Issue | Fix |
|--------|-------|-----|
| WARN | Notes sync stale (>24h) | Open the notes app on the primary machine and wait for sync. Check Settings > Sync for errors. |
| WARN | Notion Sync stale (>48h) | Run: `node "<automation-script>" notion-sync`, if it fails, check `<automation-env-file>` has a valid token |
| WARN | memory.md over 140 lines | Compact the affected agent's memory |
| WARN | Daily scheduler not loaded | Run: `launchctl load ~/Library/LaunchAgents/<automation-job>.plist` |
| WARN | Daily run missed (>24h) | Run: `launchctl start <automation-job>` then `tail -f <automation-log-file>` to check for errors |
| WARN | Too many session logs (>30) | Archive the old ones |
| FAIL | Notion token invalid/missing | Create integration at https://www.notion.so/profile/integrations, paste token in `<automation-env-file>` |
| FAIL | Scripts directory missing | `system/scripts/` was deleted, needs investigation and rebuild |
| FAIL | automation config missing/corrupt | Rebuild from the automation script's defaults or restore from git |
| INFO | Backfill incomplete | Normal for new vaults. Runs on the schedule you configure. No action needed. |
| INFO | Inbox has items | Triage the inbox when convenient |
| INFO | Agent has no session logs | Normal for agents not yet actively used (e.g., secondary-machine agents) |

## Scheduler Diagnostics

Check if the scheduler is loaded:
```bash
launchctl list | grep vault
```

Trigger a manual run:
```bash
launchctl start <automation-job>
```

Watch output in real-time:
```bash
tail -f <automation-log-file>
```

Verify the schedule (the hour in the plist should match your configured run time):
```bash
grep -A5 StartCalendarInterval ~/Library/LaunchAgents/<automation-job>.plist
```

Reload after editing plist:
```bash
launchctl unload ~/Library/LaunchAgents/<automation-job>.plist
launchctl load ~/Library/LaunchAgents/<automation-job>.plist
```

## Deep Check Procedures

### Folder Structure Audit

Scan for files outside conventional folders:
```bash
find "<HOME>/Documents/<NOTES_ROOT>/" -name "*.md" -maxdepth 1
```
Any .md files at the vault root (outside known folders) are rogue and should be triaged.

### Frontmatter Coverage

Sample 20 random .md files and check for:
- `type` field (required)
- `created` field (required)
- `modified` field (required)
- `tags` field (required)

Report as percentage: "85% coverage (17/20 files have valid frontmatter)"

Files most likely to lack frontmatter: old inbox items, manually created notes, imported content.

### Orphaned Files

Check for .md files not referenced by any index or wikilinked from other notes. Common in:
- `resources/` after migration
- `areas/` when index.md wasn't updated
- `inbox/` items that were manually moved without updating references

### Config Validation

Parse the automation config and verify:
- Every configured remote page id is well formed
- Every mapped folder path exists on disk
- The backfill flag matches the actual state
- No entry is registered twice across the configured collections

### Duplicate Detection

Scan all .md files for `notion-id` in frontmatter. Report any duplicates, these would cause sync conflicts where one file overwrites another.

## Common Resolution Patterns

**"Notion sync keeps failing"**
1. Check token: `grep NOTION_TOKEN <automation-env-file>`
2. Test token: `curl -s -H "Authorization: Bearer YOUR_TOKEN" -H "Notion-Version: 2022-06-28" https://api.notion.com/v1/users/me`
3. If 401: token expired or revoked. Create new integration.
4. If 403: integration not shared with the pages. Share pages with integration in Notion.

**"launchd job never runs"**
1. The machine must be awake at the scheduled hour
2. Check `RunAtLoad` is false (correct, avoids running on every login)
3. Verify plist is in `~/Library/LaunchAgents/` (not `/Library/LaunchAgents/`)
4. Check log: `tail -20 <automation-log-file>`
