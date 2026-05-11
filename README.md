# Understand-Anything Metrics

Daily snapshot of traffic stats for [Lum1104/Understand-Anything](https://github.com/Lum1104/Understand-Anything).

GitHub's Traffic API only retains the last 14 days. This repo runs a cron'd GitHub Action that pulls clones + views every day and accumulates them into `data/traffic.json` for the full project history.

## Data shape

`data/traffic.json`:

```json
{
  "clones": [
    { "timestamp": "2026-05-11T00:00:00Z", "count": 12, "uniques": 8 }
  ],
  "views": [
    { "timestamp": "2026-05-11T00:00:00Z", "count": 45, "uniques": 30 }
  ],
  "lastUpdated": "2026-05-11T03:00:00Z"
}
```

`count` = total clones / page views that day. `uniques` = unique cloners / visitors that day.

## How it works

`.github/workflows/traffic.yml` runs daily at 03:00 UTC (also `workflow_dispatch` for manual triggers). It calls the GitHub Traffic API for the source repo, merges the returned 14-day window into the existing history (deduplicated by timestamp, newer wins), and commits if anything changed.

The traffic API requires push access to the source repo, so the workflow uses a PAT stored as the `TRAFFIC_TOKEN` repo secret.

## License

MIT
