# FoodTracker — Ledger

> Frictionless, personal calorie and macro tracker with exercise-adjusted allowances, LLM-assisted food capture, and ADHD-friendly design. Built on a fork of OpenNutriTracker (Flutter).

## Status

**Phase:** Stage 2 — Feature Development (Phase 6 next)
**Last updated:** 2026-04-07

## Subsystems

| Subsystem | Status | Doc |
|-----------|--------|-----|
| Core Domain | Not started (Phase 6) | [link](subsystems/core-domain.md) |
| Today Dashboard | Not started (Phase 7) | [link](subsystems/today-dashboard.md) |
| Friction Foundations | Not started (Phase 8) | [link](subsystems/friction-foundations.md) |
| Apple Health | Not started (Phase 9) | [link](subsystems/apple-health.md) |
| Claude API Integration | Not started (Phase 11) | [link](subsystems/claude-api-integration.md) |
| Database (drift/SQLite) | Complete (Phase 5) | [link](subsystems/database.md) |

## Key Decisions

See [decisions/LOG.md](decisions/LOG.md) for the full decision log.

## Linked Projects

| Project | Relationship | Notes |
|---------|-------------|-------|
| Momentum | related-to | Both track health/habit data, potential data sharing |
| Mealie | recipe-library | Self-hosted recipe manager (private fork) on the mini. FoodTracker pulls recipes + nutrition from Mealie's API and caches them for offline logging — likely replaces the Phase 6 recipe-builder scope. Integration design: ~/Dev/Mealie/ledger/subsystems/foodtracker-integration.md |

## Open Questions

- When was this project last actively worked on?
- Are Phases 4 and 5 fully verified on device?

## Notes

Extensive existing documentation: vision statement, technical brief, workflow guide, and detailed roadmap already exist at the project root. The ledger ROADMAP.md is a migration of the existing ROADMAP.md into ledger format.
