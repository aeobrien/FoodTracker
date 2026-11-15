# OpenNutriTracker Codebase Audit

**Date:** 2026-02-20
**Audited version:** Fork of simonoppowa/OpenNutriTracker, commit 2b893cb (2025-05-24)
**Flutter version:** 3.41.2 (upgraded from project's 3.27.1)

---

## Executive Summary

OpenNutriTracker is a well-structured Flutter app with clean architecture (BLoC + feature-first) but significant limitations for our plans. The **three critical findings** are:

1. **The database (Hive) is fundamentally wrong for our use case.** It's a key-value store being used for relational data. Every query is a full scan. There are no indexes. The locale-dependent date keys are a data corruption risk. Recipes, quick-add, food-item references, and computed snapshots are impossible without major restructuring. **Recommendation: Migrate to drift/SQLite.**

2. **Food data is denormalised into every log entry.** There is no standalone "food items" table. Each intake embeds a complete copy of the food's nutritional data. This makes `lastUsedGrams`, food correction propagation, and local food search impossible without restructuring. **This is the single biggest schema problem.**

3. **The diary uses shame-based visual language.** Red calendar dots and red summary cards for "bad" days. BMI prominently displayed on the profile. This directly conflicts with the vision's no-shame, ADHD-friendly principles. **Must be changed.**

**Overall assessment:** Usable as a base, but requires significant data layer work before features can be built. The UI layer and BLoC architecture are solid and extensible. The barcode scanner, OFF integration, and basic logging flow all work. We should keep the architecture and rework the data layer.

---

## 1. Data Layer

### Database: Hive (NoSQL key-value store, AES-encrypted)

**5 Hive boxes, 15 registered type adapters.**

| Box | Key Strategy | Contents |
|-----|-------------|----------|
| ConfigBox | Single key `"ConfigKey"` | App config (theme, units, macro %, disclaimer) |
| UserBox | Single key `"UserKey"` | User profile (birthday, height, weight, gender, goal, PAL) |
| IntakeBox | Auto-increment int | Food log entries (each embeds a full MealDBO copy) |
| UserActivityBox | Auto-increment int | Activity log entries |
| TrackedDayBox | Locale-formatted date string | Materialised daily stats (running totals) |

### Critical Schema Issues

| Issue | Impact | Severity |
|-------|--------|----------|
| **No food items table** — food data embedded in each intake | Can't implement lastUsedGrams, favourites, food correction, local search | Critical |
| **No recipe model** | Can't build recipe feature | Critical |
| **No quick-add type** — all intakes require a full MealDBO | Can't implement quick-add estimates | Critical |
| **No isDraft flag** on intakes | Can't preserve interrupted logging state | High |
| **No computedSnapshot** on intakes | Nutrition computed at runtime, not stored at log time | High |
| **Locale-dependent date keys** in TrackedDayBox | Changing locale makes existing data unreachable | High |
| **Running-total DailyStats** — increment/decrement, never reconciled | Crash mid-operation causes permanent drift | Medium |
| **Full-scan queries** — no secondary indexes on any box | Performance degrades linearly with data volume | Medium |

### Query Performance

| Operation | Method | Cost |
|-----------|--------|------|
| Entries for a date | Full scan with isSameDay filter (x4 per day view) | O(n) |
| Sum calories for day | Pre-maintained counter (fragile) | O(1) |
| Recent foods | Full scan + sort + dedup | O(n log n) |
| Weekly range | Full scan with date range filter | O(n) |
| Search foods by name | Not implemented locally (API only) | N/A |

### Hive Viability Verdict

**Not viable for the target feature set.** Hive lacks: secondary indexes, relational queries, full-text search, atomic multi-record updates, and range queries. Every planned feature (recipes, quick-add, food correction, favourites, weekly aggregation) would require increasingly painful workarounds.

**Recommendation:** Migrate to **drift** (type-safe SQLite wrapper). This gives us: relational queries, reactive streams for live UI updates, proper migrations, FTS5 for food search, and efficient date-range queries.

---

## 2. Features & UI Layer

### Home Dashboard
- Large circular gauge showing "kcal left" (caps at 0, no red for overages — good)
- Macro progress circles for carbs/fat/protein
- Four meal groups (breakfast/lunch/dinner/snack) as horizontal scrolling cards
- Tap entry → edit dialog, long-press → delete, drag-to-delete zone
- **No recents/favourites on home screen**
- **No quick-add shortcut**

### Food Logging (Add Meal)
- Three tabs: Products (OFF), Food (FDC/Supabase), Recently Added
- Search requires submission (not live search)
- Meal type MUST be chosen before searching (extra decision point)
- Default quantity: 100g (rarely what anyone eats, forces manual adjustment)
- **No portion memory** — doesn't remember last-used amount
- **No favourites**
- Custom food creation: 6+ taps through dialogs and a 9-field form
- Dead-end on "no results" — no prompt to create custom

### Barcode Scanner
- Uses mobile_scanner (ML Kit on iOS)
- On success: navigates to meal detail
- **On failure: dead end.** Shows error dialog with retry only. No fallback to text search.

### Diary / Calendar
- Month calendar view with day dots
- **SHAME-BASED: Red dots for "bad" days** (>500 over or >1000 under goal)
- **SHAME-BASED: Red summary card** for over-target days
- No weekly view, no list view
- Calendar range: 356 days (should be 365 — bug)

### Profile
- **BMI prominently displayed** with nutritional status classification (judgmental)
- No custom calorie target — everything calculated from body stats + goal
- Binary gender only (Male/Female)

### Onboarding
- **6 mandatory screens** before first use (gender, age, height, weight, activity, goal)
- No skip option
- Heavy upfront commitment

### Settings
- Theme, units, calculations, export/import, disclaimer, privacy, about
- Export: ZIP of 3 JSON files (intakes, activities, tracked days)
- **No notification settings** (no notification system exists)
- **No calorie goal override**

### Tap Counts

| Action | Taps from Home |
|--------|---------------|
| Log food (search) | 4 minimum |
| Log food (barcode) | 4 minimum |
| Log food (recent) | 4 minimum |
| Create custom food | 6+ |
| Quick-add estimate | Not possible |
| Delete entry | 2 (long-press + confirm) |
| Edit entry amount | 2 (tap + dialog) |

### Shame-Based Visual Language Found

1. Calendar day dots: red for "bad" days
2. Daily calorie summary card: red background for over-target
3. BMI display with nutritional status classification
4. (Delete zone uses red — contextually appropriate, not shame-based)

---

## 3. Dependencies

### Must Remove
| Package | Reason |
|---------|--------|
| `supabase_flutter` | Used only for one FDC search path. Direct FDC API exists. Conflicts with local-first goal. Requires env secrets. |

### Can Remove
| Package | Reason |
|---------|--------|
| `sentry_flutter` | Opt-in crash reporting. All errors already logged via Logger. No DSN configured. |

### Must Add (for Stage 2)
| Package | Purpose |
|---------|---------|
| `drift` + `sqlite3_flutter_libs` | SQLite database (replacing Hive) |
| `health` | Apple HealthKit integration |
| `camera` or similar | Photo capture for LLM label extraction |
| `flutter_local_notifications` | Meal reminder notifications |
| `anthropic_sdk_dart` or `http` | Claude API integration |

### Supabase Removal Plan
Supabase is used in exactly one place: `SpFdcDataSource` provides FDC food search via a Supabase-hosted Postgres table. The direct `FDCDataSource` already exists and can replace it. Removal steps:
1. Route `searchFDCFoodByString()` through direct FDC API instead of Supabase
2. Delete `sp_fdc_data_source.dart` and its DTOs
3. Remove Supabase from locator, env, and pubspec

---

## 4. Tests

**Coverage: ~5%.** 5 test files for 256 source files.

| Test File | Tests | Status |
|-----------|-------|--------|
| `calorie_goal_calc_test.dart` | TDEE + goal calculation | Looks valid |
| `tdee_calc_test.dart` | TDEE equation for M/F | Looks valid |
| `met_calc_test.dart` | MET-based calorie burn | Looks valid |
| `intake_repository_test.dart` | Recent intake ordering | Fragile (writes to CWD) |
| `widget_test.dart` | Dashboard widget render | Looks valid |

**Untested:** All 11 BLoCs, all data sources, all use cases except calculation helpers, all screens, API response parsing, export/import, barcode flow, onboarding.

---

## 5. Code Quality

### Good
- Clean Architecture with feature-first organisation
- Consistent naming (snake_case files, PascalCase classes)
- Structured logging via `logging` package (no `print()` calls)
- Consistent error handling pattern (log + report + propagate)

### Issues
- **Fragile DI:** `locator()` with positional args — HomeBloc takes 10 untyped `locator()` calls. Adding/removing a param causes silent runtime failure.
- **No abstract interfaces** on repositories or data sources — testing requires concrete implementations
- **Misplaced file:** `user_activity_dbo.dart` in `data_source/` instead of `dbo/`
- **Typos:** `intiLogger()`, `add_user_activity_usercase.dart`
- **No retry/backoff** on API calls
- **FDC data source** doesn't check HTTP status codes before parsing

### Localisation
3 languages: English (complete, 415 keys), German (405 keys), Turkish (412 keys). ~60% of keys are physical activity descriptions.

---

## 6. Decisions Required for Stage 2

### Decision 1: Database Migration (Hive → drift)

**Recommendation: Yes, migrate.**

The cost of keeping Hive and working around its limitations for every new feature (recipes, quick-add, food items table, snapshots, favourites, weekly queries) will exceed the upfront cost of migration. drift gives us everything we need: relational queries, reactive streams, FTS5 search, proper migrations, and type safety.

**Migration strategy:**
- Build the new drift schema alongside Hive
- Implement a one-time migration that reads all Hive data and writes to SQLite
- Keep Hive-reading code temporarily for migration, then remove

### Decision 2: Supabase Removal

**Recommendation: Remove.** Route FDC search through direct API. Eliminates env secret dependency and aligns with local-first.

### Decision 3: Sentry Removal

**Recommendation: Remove.** No DSN configured, all errors already logged. Can add back later if needed.

### Decision 4: How Much UI to Keep vs. Rebuild

**Recommendation: Keep the structure, modify the content.**
- Keep: BLoC architecture, feature-first organisation, navigation patterns, Material 3 theming
- Modify: Home dashboard (add allowance breakdown, remove activity section, add recents), calendar (remove shame colours), meal detail (add portion memory, default to last-used amount)
- Remove: BMI display, activity logging feature (replaced by HealthKit), Supabase integration
- Add: Quick-add screen, recipe builder, LLM capture flow, settings for new features

### Decision 5: Onboarding Simplification

**Recommendation: Reduce to 2-3 screens max.** Ask only: calorie target (with a "calculate for me" option), and macro split. Skip gender/age/height/weight unless the user wants auto-calculation. Let them start logging immediately.

---

## 7. Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Drift migration introduces data loss | Medium | High | Build migration with validation; keep Hive backup until verified |
| Hive encryption key loss during migration | Low | High | Read existing key from flutter_secure_storage; use same for SQLite if needed |
| OFF API coverage insufficient for UK | Medium | Medium | LLM label capture as fallback; local food creation is first-class |
| Flutter framework text selection crash | Known | Low | Avoid long-press on text fields; will be fixed in future Flutter update |
| Large codebase changes break existing features | Medium | Medium | Build comprehensive tests before and during migration |
