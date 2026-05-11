# Understand-Anything Metrics

Daily snapshot of traffic stats for [Lum1104/Understand-Anything](https://github.com/Lum1104/Understand-Anything).

GitHub's Traffic API only retains the last 14 days. This repo runs a cron'd GitHub Action that pulls clones + views every day and accumulates them into `data/traffic.json` for the full project history.

## Data shape

`data/traffic.json`:

```json
{
  "totals": {
    "clones": 25431,
    "uniqueClonerDays": 12384,
    "peakDailyClones": 3324,
    "peakDailyUniqueCloners": 1420,
    "views": 67890,
    "uniqueVisitorDays": 34567,
    "peakDailyViews": 5210,
    "peakDailyUniqueVisitors": 1850,
    "daysTracked": 16,
    "firstDay": "2026-04-26T00:00:00Z",
    "lastDay": "2026-05-11T00:00:00Z"
  },
  "clones": [
    { "timestamp": "2026-04-26T00:00:00Z", "count": 1306, "uniques": 391 }
  ],
  "views": [
    { "timestamp": "2026-04-26T00:00:00Z", "count": 45,   "uniques": 30  }
  ],
  "lastUpdated": "2026-05-11T03:00:00Z"
}
```

### Per-day entries

- `count` — total clones / page views that day.
- `uniques` — unique cloners / visitors that day.

### Totals

Recomputed from the full history every run.

| Field | Meaning |
|---|---|
| `clones` | All-time sum of daily clone counts. Each clone is counted once. |
| `uniqueClonerDays` | Sum of daily-`uniques`. **Not** total unique users — GitHub doesn't expose cloner identity, so the same person cloning on 5 days adds 5 to this number. Useful as a "cloner-engagement-days" proxy. |
| `peakDailyClones` | Highest single-day total clones. |
| `peakDailyUniqueCloners` | Highest single-day unique-cloner count. Functions as a lower bound on "ever cloned by N distinct users". |
| `views` / `uniqueVisitorDays` / `peakDailyViews` / `peakDailyUniqueVisitors` | Same idea for page views. |
| `daysTracked` | Length of the `clones` array. |
| `firstDay` / `lastDay` | Range of recorded days (ISO timestamps). |

## How it works

`.github/workflows/traffic.yml` runs daily at 03:00 UTC (also `workflow_dispatch` for manual triggers). It calls the GitHub Traffic API for the source repo, merges the returned 14-day window into the existing history (deduplicated by timestamp, newer wins), and commits if anything changed.

The traffic API requires push access to the source repo, so the workflow uses a PAT stored as the `TRAFFIC_TOKEN` repo secret.

## License

MIT
