# Phase 1: Smoke Test — OpenNutriTracker on iPhone 14 Pro

**Date:** 2026-02-20
**Device:** iPhone 14 Pro (Mirador), iOS 26.2.1
**App version:** OpenNutriTracker 1.0.0+41 (forked, hive_ce migration)

## Instructions

Work through each section. For each item, note:
- **Pass** — works as expected
- **Fail** — broken or crashes
- **Partial** — works but with issues (describe)
- **N/A** — can't test (explain why)

Don't long-press on text fields (known Flutter framework crash on iOS 26).

---

## 1. Onboarding

- [ ] App launches without crash
- [ ] Onboarding wizard appears on first run
- [ ] Can enter personal details (height, weight, age, gender)
- [ ] Can set activity level
- [ ] Can set a calorie/weight goal
- [ ] Onboarding completes and reaches the main screen
- **Notes:**

## 2. Home Screen (Dashboard)

- [ ] Home screen loads and shows today's data
- [ ] Calorie goal is displayed
- [ ] Macro breakdown is visible (protein/carbs/fat)
- [ ] Progress indicators/bars are present
- [ ] The FAB (floating action button) or "add" button is visible
- [ ] Tapping the add button opens options (add food / add activity)
- **Notes:**

## 3. Food Logging — Search

- [ ] Can navigate to "Add Meal" / food search screen
- [ ] Search field is usable (can type, results appear)
- [ ] Searching for "chicken" returns results
- [ ] Searching for "banana" returns results
- [ ] Searching for "milk" returns results
- [ ] Results show food name, brand, and calorie info
- [ ] Tapping a result opens a detail/quantity screen
- [ ] Can set a quantity (grams or servings)
- [ ] Can confirm and log the food
- [ ] Logged food appears on the home screen / diary
- [ ] Daily totals update after logging
- **Notes:**

## 4. Food Logging — Barcode Scanner

- [ ] Scanner screen opens (camera activates)
- [ ] Can scan a barcode on a packaged food
- [ ] **Scan 5 UK products and record results:**

| Product | Barcode found? | Data complete? | Notes |
|---------|---------------|----------------|-------|
| 1.      |               |                |       |
| 2.      |               |                |       |
| 3.      |               |                |       |
| 4.      |               |                |       |
| 5.      |               |                |       |

- [ ] When barcode IS found: can log the food from the result
- [ ] When barcode is NOT found: what happens? (describe)
- **Notes:**

## 5. Diary / Calendar

- [ ] Can navigate to the Diary tab
- [ ] Today's entries are visible
- [ ] Can navigate to a previous day
- [ ] Can navigate to a future day (or is it blocked?)
- [ ] Past day shows any previously logged data
- [ ] Calendar navigation is intuitive
- **Notes:**

## 6. Editing & Deleting Log Entries

- [ ] Can tap on a logged entry to view it
- [ ] Can edit the quantity of a logged entry
- [ ] Can delete a logged entry
- [ ] Daily totals update after edit/delete
- **Notes:**

## 7. Activity Tracking

- [ ] Can navigate to add an activity
- [ ] Activity list/search is available
- [ ] Can log an activity
- [ ] Logged activity appears somewhere in the app
- **Notes:**

## 8. Profile Screen

- [ ] Profile tab loads
- [ ] Shows user info (from onboarding)
- [ ] Can edit profile details
- **Notes:**

## 9. Settings

- [ ] Settings screen accessible
- [ ] Can change calorie/macro goals
- [ ] Can change theme (light/dark)
- [ ] Data export option exists
- [ ] Data import option exists
- [ ] Can export data (does it produce a file?)
- **Notes:**

## 10. General UX Observations

Answer these in your own words:

- **How many taps to log a food from search?**
- **How many taps to log a food from barcode?**
- **Does the app feel fast or sluggish?**
- **Is there any "shame language" (red warnings, negative framing for going over target)?**
- **Are there any streak mechanics or gamification?**
- **Is there a recents or favourites feature?**
- **Is there any meal grouping (breakfast/lunch/dinner)?**
- **What's your overall first impression?**
- **What's the single most annoying thing about the current app?**
- **What's the single best thing about the current app?**

---

## 11. Crash / Bug Log

Record any crashes or bugs you encounter:

| # | What you did | What happened | Severity |
|---|-------------|---------------|----------|
| 1 |             |               |          |
| 2 |             |               |          |
| 3 |             |               |          |

---

## Summary

After completing all sections, give an overall assessment:

- **Usable as a base to build on?** (Yes / No / With significant changes)
- **Biggest risks for our plans?**
- **Features that work well enough to keep as-is?**
- **Features that need significant rework?**
