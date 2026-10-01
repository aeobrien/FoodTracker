# FoodTracker — Manual Test Workflow

Structured test plan organised into ~15-minute sessions. Work through on your phone with the app open. Tick off as you go. Items marked **(PARTIAL)** need extra attention — the audit found incomplete implementations.

---

## Session 1: Core Dashboard (~15 min)

### Today Screen — Basic Display
- [ ] Open app — Today screen loads without delay
- [ ] Today's date is visible on the screen
- [ ] Calorie allowance breakdown is shown (base, earned, allowance, consumed, remaining) **(PARTIAL — verify all five components appear)**
- [ ] If over target: displays "X over target" in neutral text, NOT red/warning colours **(PARTIAL — check styling carefully)**
- [ ] Weekly context line visible ("This week: X under/over target")
- [ ] Macro progress bars display for protein, carbs, fat

### Today Screen — Edge Cases
- [ ] Force-quit and reopen — screen loads quickly from cache **(PARTIAL — note any visible recomputation delay)**
- [ ] With zero entries logged today — displays cleanly, no errors
- [ ] With no exercise data — base allowance shown correctly, no "earned" confusion

---

## Session 2: Logging a Meal (~15 min)

### Search and Add Food
- [ ] Tap to add food — search screen opens
- [ ] Search for a common food (e.g. "banana") — results appear
- [ ] Select a food — quantity entry screen shown
- [ ] Default quantity shown **(PARTIAL — check if it defaults to 100g or something smarter)**
- [ ] Enter a quantity and save — returns to Today screen
- [ ] New entry appears in the meal log
- [ ] Calorie/macro totals update immediately

### Meal Grouping
- [ ] Log items at different times — check if they group by meal **(PARTIAL — grouping logic may be incomplete)**
- [ ] Meal labels (Breakfast, Lunch, etc.) visible in the log

### Editing an Entry
- [ ] Tap an existing entry — can you edit it? **(PARTIAL — EditMealScreen exists but may not be inline)**
- [ ] Change the quantity — totals update after save
- [ ] Note: inline editing (tap-to-adjust in the list) is NOT yet implemented

---

## Session 3: Recents and Favourites (~15 min)

### Recents
- [ ] Open add-food screen — recents section visible
- [ ] Previously logged foods appear, deduplicated
- [ ] Tap a recent item — adds with one tap (no extra screens)
- [ ] Same food logged multiple times only appears once in recents

### Favourites
- [ ] Look for a way to star/favourite a food **(PARTIAL — BLoC exists, UI may be incomplete)**
- [ ] If favourite mechanism exists: favourite a food, check it appears above recents
- [ ] Note any missing UI elements or broken flows

### Portion Memory
- [ ] Log "banana" at 150g. Close and reopen the add screen.
- [ ] Search "banana" again — does it remember 150g? **(PARTIAL — last-quantity NOT saved)**
- [ ] Note: this is expected to still show 100g default

---

## Session 4: Apple Health + Exercise (~15 min)

### HealthKit Connection
- [ ] Go to Settings — HealthKit status visible (`HealthDebugScreen`)
- [ ] Permissions are granted (or grant them now)
- [ ] Active calories from Apple Health are being read

### Exercise-Adjusted Allowance
- [ ] Do some exercise (or check a day with exercise data)
- [ ] Today screen shows earned calories with 0.75 multiplier applied
- [ ] Allowance = base + (active cals x 0.75)
- [ ] Exercise multiplier setting is adjustable in Settings

### Edge Cases
- [ ] Deny HealthKit permission — app handles gracefully, no crash
- [ ] Check if earned calories cache properly **(PARTIAL — caching incomplete)**
- [ ] Switch away from app and back — does exercise data refresh? **(expected: NO, foreground refresh not implemented)**

---

## Session 5: Quick-Add (~10 min)

### Quick-Add Flow
- [ ] Find the quick-add entry point (may not be prominent on home yet) **(home button NOT implemented)**
- [ ] Quick-add screen opens — can enter kcal, optional macros, label
- [ ] Save a quick-add entry — appears in today's log
- [ ] Quick-add entry counts toward daily totals
- [ ] Check if quick-add entries look different from regular entries **(expected: NO visual distinction yet)**

### Edge Cases
- [ ] Quick-add with only kcal (no macros, no label) — saves correctly
- [ ] Quick-add with 0 kcal — what happens?

---

## Session 6: LLM Label Capture (~15 min)

### Photo Capture
- [ ] Find the label capture flow **(PARTIAL — photo capture incomplete)**
- [ ] Take a photo of a nutrition label
- [ ] Wait for Claude API processing — extraction happens
- [ ] Confirmation screen shows extracted nutrition data (editable form)
- [ ] Atwater validation runs on extracted values

### Save and Use
- [ ] Edit a value on the confirmation screen — fields are editable
- [ ] Save as a food item **(PARTIAL — check if it saves correctly)**
- [ ] Search for the saved food — it appears in results
- [ ] Log a portion of the saved food

### Edge Cases
- [ ] Photo of something that isn't a nutrition label — how does it handle?
- [ ] No internet connection — **(expected: no offline fallback yet)**
- [ ] Note: "Not found" flow is NOT implemented

---

## Session 7: Recipes (~15 min)

### Manual Recipe
- [ ] Open recipe builder
- [ ] Add ingredients with quantities
- [ ] Set servings count
- [ ] Save recipe — nutrition calculated correctly
- [ ] Log a portion of the recipe — appears in today's log

### LLM Recipes
- [ ] Use text-to-recipe — describe a recipe in words, LLM creates it
- [ ] Use URL-to-recipe — paste a recipe URL, LLM extracts it
- [ ] Both save correctly with proper nutrition data

### Recipe Management
- [ ] View saved recipes list
- [ ] Edit a recipe — changes apply
- [ ] Delete a recipe — confirm it doesn't affect past log entries
- [ ] Check if recipes appear in recents/favourites **(PARTIAL)**

---

## Session 8: Notifications + Weekly (~10 min)

### Notifications
- [ ] Notification scheduling is active — check iOS notification settings
- [ ] Notifications use warm, non-judgmental language
- [ ] Tap a notification — deep links into the app correctly
- [ ] Per-meal notification toggles in settings **(PARTIAL — check completeness)**

### Weekly View
- [ ] Find the weekly summary view **(PARTIAL)**
- [ ] Shows 7-day data
- [ ] Weekly context appears on Today screen (already checked in Session 1)

---

## Session 9: Settings (~15 min)

### Target Settings
- [ ] Calorie target — can view and change **(PARTIAL — check if it saves/applies)**
- [ ] Macro targets — can set protein/carbs/fat goals **(PARTIAL)**
- [ ] Exercise multiplier — adjustable, default 0.75
- [ ] Day boundary — configurable start hour

### API and Storage
- [ ] Claude API key — stored securely, can update
- [ ] HealthKit status screen — shows connection state

### Data Management
- [ ] Data export — **(PARTIAL — check what formats work, JSON and/or CSV)**
- [ ] Data import — **(PARTIAL — check if import from JSON works)**
- [ ] Theme switching — light/dark/system all work
- [ ] Note: metric/imperial units toggle NOT implemented

### Edge Cases
- [ ] Change calorie target — does Today screen update immediately?
- [ ] Change day boundary — does it affect which entries appear for "today"?
- [ ] Export with no data — handles gracefully

---

## Session 10: Deletion and Edge Cases (~10 min)

### Deleting Entries
- [ ] Delete a meal entry from today's log
- [ ] Totals update after deletion
- [ ] Calendar dots update after all entries deleted for a day **(expected: NOT working — cleanup not implemented)**
- [ ] Delete confirmation exists? **(expected: NO — not yet implemented, uses old drag-to-delete)**

### General Edge Cases
- [ ] Log food for a past date — retroactive logging works
- [ ] App behaviour around 2 AM (day boundary) — correct date assigned
- [ ] Large quantities (e.g. 99999g) — no crashes
- [ ] Search with no results — clean empty state
- [ ] Rapid back-and-forth navigation — no crashes or stuck states

---

## Notes and Bugs Found

Use this section to capture anything you find during testing.

### Bugs
| # | Session | Description | Severity |
|---|---------|-------------|----------|
| | | | |

### Missing Features Confirmed
| Feature | Status | Notes |
|---------|--------|-------|
| Calendar dot cleanup (2.1.7) | Not found | |
| Delete confirmation (2.1.8) | Not found | |
| Quick increment buttons (3.1.2) | Not found | |
| Swipe to delete with undo (3.1.7) | Not found | |
| Draft state persistence (3.1.8) | Not found | |
| Foreground refresh (4.1.6) | Not found | |
| Background observer (4.1.7) | Not found | |
| Quick-add visual distinction (5.1.3) | Not found | |
| Quick-add home button (5.1.4) | Not found | |
| LLM "not found" flow (6.1.7) | Not found | |
| Offline fallback (6.1.8) | Not found | |
| Units setting (9.1.10) | Not found | |

### Partial Implementations — Issues Found
| Feature | What Works | What's Broken/Missing |
|---------|-----------|----------------------|
| | | |

### General Notes
- 
- 
- 

---

## Session 11: New Features (15 min)

### Quick Increment Buttons (Task 3.1.2)
- [ ] Open a food item detail screen (tap a search result or recent)
- [ ] Verify +10g, +50g, +100g, -10g, -50g, -100g chips are visible when unit is grams
- [ ] Tap +50g — quantity increases by 50
- [ ] Tap the half button (1/2 symbol) — quantity is halved
- [ ] Tap half again on a small number — check it does not go below 0
- [ ] For a food with serving data: verify "1 serving" chip appears when unit is not "serving"
- [ ] Tap "1 serving" — quantity jumps to the food's serving size
- [ ] Switch to serving unit — verify serving-specific chips (+0.5 srv, +1 srv) plus half button
- [ ] Switch to a liquid food — verify ml labels on increment chips

### Swipe to Delete with Undo (Task 3.1.7)
- [ ] On the home screen, swipe down on a meal card — card dismisses
- [ ] Verify "Item deleted" snackbar appears with an "Undo" button
- [ ] Tap "Undo" before timeout — item reappears in the list
- [ ] Swipe a different item down — do NOT tap undo
- [ ] Wait 5 seconds — snackbar auto-dismisses, item stays deleted
- [ ] Verify calorie totals update after deletion

### Calendar Dot Cleanup (Task 2.1.7)
- [ ] Go to Diary tab — note which days have calendar dots
- [ ] Delete all meals from one day (use swipe or long-press delete)
- [ ] Return to Diary tab — verify the dot has disappeared for that day
- [ ] Add a meal back to that day — verify the dot reappears

### Delete Confirmation Dialog (Task 2.1.8)
- [ ] Long-press a food entry on the home screen
- [ ] Verify confirmation dialog shows the specific food name (e.g., "Delete Banana?")
- [ ] Tap CANCEL — dialog closes, item is NOT deleted
- [ ] Long-press the same item again, tap DELETE — item is deleted
- [ ] Long-press a quick-add entry — verify dialog shows the quick-add label
- [ ] Long-press a recipe entry — verify dialog shows the recipe name

### Quick-Add Visual Distinction (Task 5.1.3)
- [ ] Add a quick-add entry (Quick Add screen, enter calories, save)
- [ ] On the home screen, find the quick-add card in the meal list
- [ ] Verify a small lightning bolt badge appears next to the kcal label
- [ ] Compare with a regular food entry — regular entries should NOT have the badge
- [ ] Compare with a recipe entry — recipe entries should NOT have the badge

### Units System (Task 9.1.10)
- [ ] Go to Settings > Units
- [ ] Switch to Imperial — confirm setting saves
- [ ] Return to home screen — verify food amounts display in oz/fl oz
- [ ] Go to meal detail screen — verify unit dropdown offers oz/fl oz options
- [ ] Add a meal in imperial units — verify it saves correctly
- [ ] Switch back to Metric — verify amounts display in g/ml
- [ ] Verify the stored values have not changed (same kcal totals)
