# Apple Health Integration

## Overview

Reads Active Energy Burned from HealthKit to calculate exercise-adjusted calorie allowance. The motivational core of the app: "I moved more, so I can eat more." Configurable multiplier (default 75%) accounts for Apple Watch estimation error.

## Status

**Current state:** Not started (Phase 4)
**Last updated:** 2026-04-07

## Connections

| Connects To | Interface | Notes |
|-------------|-----------|-------|
| Core Domain | Allowance model | Active calories × multiplier = earned |
| Today Dashboard | UI display | Shows earned calories in breakdown |
| Database | DailyStats cache | Caches active_calories to avoid repeated HealthKit reads |

## Design Notes

- Uses `health` Flutter plugin + HealthKit entitlement
- Foreground refresh: re-read on app resume
- Background observer (stretch): Swift platform channel for HKObserverQuery
- Graceful denial: if permissions denied, allowance = base target, no error states

## Open Questions

- None currently
