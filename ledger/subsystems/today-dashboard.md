# Today Dashboard

## Overview

The primary screen users see every day. Shows allowance breakdown (base → earned → allowance → consumed → remaining), macro progress bars, meal-grouped log, and weekly context. Must load instantly from cached DailyStats.

## Status

**Current state:** Not started (Phase 2)
**Last updated:** 2026-04-07

## Connections

| Connects To | Interface | Notes |
|-------------|-----------|-------|
| Core Domain | Function calls | Calculation logic for all displayed values |
| Database | BLoC → Repository | Reads DailyStats, log entries |
| Friction Foundations | UI integration | Inline editing, swipe delete, favourites |

## Design Notes

- Neutral overage display — "X over target" in calm styling, never red/warning
- Meal slots auto-suggested by time, never mandatory
- Weekly context line: "This week: Y under/over target"
- Instant load from materialised daily_stats table

## Open Questions

- None currently
