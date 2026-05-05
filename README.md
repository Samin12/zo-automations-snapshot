# zo-automations-snapshot

Redundant, git-tracked snapshot of all active scheduled agents on Samin's Zo Computer.

## Why

Zo's UI lists scheduled agents but does not always reflect runtime health — agents can be flagged `active=true` while silently failing to fire (e.g. when assigned an invalid model namespace like `vercel:minimax/...` instead of `zo:minimax/...`).

This repo is the redundant source of truth: a daily Zo automation re-snapshots the agent list and pushes here, so `git diff` shows drift over time.

## Structure

- `automations.json` — current snapshot of all agents, with `id`, `title`, `rrule`, `next_run`, `model`, `delivery_method`, `active`, and a 500-char instruction preview.
- `snapshots/YYYY-MM-DD.json` — daily archive of past snapshots (written by the health-check agent).
- `HEALTH-CHECK.md` — what the daily agent does and how to read drift.

## Reading drift

```bash
git log --oneline automations.json | head
git diff HEAD~1 HEAD -- automations.json
```

Look for:
- An agent silently changing model (especially to anything not in Zo's valid model list).
- An agent flipping `active: true` \u2192 `false` unexpectedly.
- `next_run` that hasn't moved forward when it should have (sign the agent is failing to fire).
- Agents disappearing entirely.

## Background

Created 2026-05-04 after the discovery that 6 of Samin's 10 agents had been silently no-firing for ~2 days due to an invalid `vercel:minimax/minimax-m2.7` model setting (the valid namespace is `zo:`). Repaired by switching to `zo:anthropic/claude-opus-4-7`.
