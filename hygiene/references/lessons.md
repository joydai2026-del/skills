# Hygiene Lessons, learned from prior cleanups

Lessons captured from real near-misses. Update this file after every `/hygiene` run
if a new trap emerges.

## Verification checklist before marking "Safe to delete"

For any folder at `~/`, `~/Documents/`, `~/projects/`, `~/dev/`:

- [ ] `which <likely-command>`, not a SDK/tool on PATH?
- [ ] `readlink <path>`, not the target of an active symlink?
- [ ] `grep -r "<folder-name>" ~/.zshrc ~/.zshenv ~/.bashrc ~/.config/zsh/`, not in shell config?
- [ ] `launchctl list | grep <folder-name>`, not a launchd agent working dir?
- [ ] `git remote -v` (if it's a repo), has a remote backup?
- [ ] Cross-ref vault project registry, not an active project?
- [ ] Cross-reference your own retired-projects list: is it explicitly retired?

If ANY check is unclear → "Needs verification" tier, never "Safe".

## Post-delete canary

After executing any deletion batch:

```bash
which flutter && which node && which python3 && which git
ls <HOME>/Documents/<NOTES_ROOT>/<NOTES_LINK> 2>/dev/null  # vault symlink intact
ls <HOME>/Documents/<NOTES_ROOT>/projects/*/        # no broken symlinks
```

If any canary fails, stop and investigate before continuing.

## Cadence

Run it on whatever monthly cadence you choose, and record where the schedule lives so the
next run can find it.
