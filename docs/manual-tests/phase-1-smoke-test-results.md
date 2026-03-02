# Phase 1: Smoke Test Results

**Date:** 2026-02-20
**Tester:** Aidan

## Results Summary

| Section | Result | Notes |
|---------|--------|-------|
| 1. Onboarding | Pass | No decimal height input (143.5cm). Feet mode doesn't support inches (5ft but not 5ft 10in). |
| 2. Home Screen | Pass | Today's date not shown on dashboard (shown on diary page only). |
| 3. Food Search | **Fail** | "Error while fetching product data" on all searches. Likely Supabase dummy credentials. |
| 4. Barcode Scanner | Pass | Works well. Scan + 1 tap to log. |
| 5. Diary/Calendar | Pass | Future days not blocked (not necessarily a problem). |
| 6. Edit/Delete | Pass | |
| 7. Activity | Pass | |
| 8. Profile | Pass | |
| 9. Settings | Pass | Export produces a file. |

## UX Observations

- Barcode log: 1 tap after scan (excellent)
- Search log: Not testable (search broken)
- Speed: occasional pauses but mostly fast
- Shame language: BMI on profile
- Streaks/gamification: None found
- Recents: Yes (foods and exercises)
- Meal grouping: breakfast/lunch/dinner/snack
- Overall impression: "Very good and intuitive", "very easy to use"
- Most annoying: Nothing found
- Best thing: Easy to use

## Bugs Found

1. Food search returns error on all queries (Supabase dummy credentials)
2. Height input doesn't support decimals or feet+inches
