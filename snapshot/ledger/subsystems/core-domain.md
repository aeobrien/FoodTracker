# Core Domain

## Overview

Pure calculation engine for the app — allowance model, macro scaling, day boundary logic, meal slot suggestion, Atwater validation, recipe nutrition, weekly aggregation, and DailyStats recomputation. No UI, no database access. All functions are pure and testable.

## Status

**Current state:** Not started (Phase 1 — next to implement)
**Last updated:** 2026-04-07

## Connections

| Connects To | Interface | Notes |
|-------------|-----------|-------|
| Today Dashboard | Function calls | Dashboard displays calculated values |
| Database | Repository layer | Repositories call domain functions for recomputation |
| Apple Health | Via allowance model | Active calories feed into earned calculation |

## Design Notes

- Allowance: `allowance = base_target + (active_calories × multiplier)`
- Macro scaling: proportional by default, per-macro override supported
- Day boundary: configurable start hour (default 2 AM)
- Atwater check: `(P×4) + (C×4) + (F×9)`, adjust for fibre. Flag >15% deviation.
- All values stored as per-100g, scaled at calculation time.

## Open Questions

- None currently
