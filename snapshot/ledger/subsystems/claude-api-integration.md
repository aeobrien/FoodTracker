# Claude API Integration

## Overview

LLM-powered features: nutrition label extraction from photos and recipe creation from text/URLs. Uses Claude API with user's own API key. Designed as an optional enhancer — core logging never depends on it.

## Status

**Current state:** Not started (Phase 6)
**Last updated:** 2026-04-07

## Connections

| Connects To | Interface | Notes |
|-------------|-----------|-------|
| Database | FoodItem creation | Extracted labels saved as reusable food items |
| Friction Foundations | "Not found" flow | Barcode miss → offer photo capture |

## Design Notes

- User's API key stored in flutter_secure_storage (not SQLite)
- EXIF stripped from photos before sending
- Client-side Atwater validation on extracted data
- Offline fallback: queue photo locally, process when online
- Also powers recipe creation (text-to-recipe, URL-to-recipe)

## Open Questions

- None currently
