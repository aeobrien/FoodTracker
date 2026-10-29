# Friction Foundations

## Overview

The features that make logging fast and frictionless — portion memory, quick increment buttons, favourites, improved recents, inline editing, swipe-to-delete with undo, draft state persistence, and meal slot auto-suggestion. These are core product, not polish.

## Status

**Current state:** Not started (Phase 3)
**Last updated:** 2026-04-07

## Connections

| Connects To | Interface | Notes |
|-------------|-----------|-------|
| Today Dashboard | UI integration | Inline editing, swipe delete on home screen |
| Database | Repository layer | Portion memory, favourites, recents queries |

## Design Notes

- Portion memory: store last_used_grams on food_items, default to it next time
- Favourites: star/pin system, shown above recents on add_meal screen
- Draft persistence: in-progress logging state survives app switches
- One-tap re-log from recents with same amount

## Open Questions

- None currently
