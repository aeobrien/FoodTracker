# Database (drift/SQLite)

## Overview

The data layer — migrated from Hive to drift (type-safe SQLite) in Phase 5. Provides typed DAOs, FTS5 search on food names, and relational queries. All other subsystems depend on this.

## Status

**Current state:** Complete (Phase 5)
**Last updated:** 2026-04-07

## Connections

| Connects To | Interface | Notes |
|-------------|-----------|-------|
| All subsystems | Repository pattern | Abstract data access via repositories |

## Design Notes

- Tables: food_items, log_entries, daily_stats, config, user_profile, user_activities, recipes, recipe_ingredients, outbox_items, label_captures, own_rows
- FTS5 index on food_items.name for local search
- Timestamps stored as UTC, day boundary applied in queries
- Food items referenced by ID in log entries, with computed nutrition snapshot for historical accuracy
- One-time Hive → SQLite migration runs on first launch after update

## Open Questions

- None currently
