# Health Check

A daily Zo automation runs at 03:30 ET (after the late-night Skool slot, before the morning slots) and:

1. Calls `list_automations` to dump the current state of all scheduled agents.
2. Writes a fresh `automations.json` to this repo.
3. Also writes a dated copy to `snapshots/YYYY-MM-DD.json`.
4. Compares yesterday's snapshot to today's. If anything material has changed, SMS Samin a 1-line summary.
5. Commits and pushes.

## Material change triggers (SMS)

- Any agent's `model` changes to a value not in Zo's documented list (`zo:*` or `byok:*`).
- An agent flips `active: true` \u2192 `false`.
- An agent's `next_run` moved backward, or did not advance from the previous snapshot.
- An agent disappears.

## Manual run

```bash
cd ~/Projects/zo-automations-snapshot
# (requires Zo runtime to refresh automations.json \u2014 the daily agent does this)
git pull --rebase
git status
```

## Failure modes that motivated this

| Date | What happened |
|---|---|
| 2026-05-04 | Discovered 6 agents using invalid model `vercel:minimax/minimax-m2.7`. They had been silently not firing for ~2 days. Zo UI showed them as `active=true` and listed a `next_run` time, masking the issue. |
