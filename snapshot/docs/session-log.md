# Session Log

## Session 1 — 2026-02-20

### Completed

**Stage 1: COMPLETE**

- **Phase 0: Dev Environment** — Flutter 3.41.2 installed, OpenNutriTracker forked (github.com/aeobrien/OpenNutriTracker), hive→hive_ce migration, intl version fix, SDK constraint update, code signing configured, app built and deployed to iPhone 14 Pro.
- **Phase 1: Smoke Test** — User tested all features on device. Barcode scanning works (1 tap after scan). Food search broken (Supabase dummy credentials). App feels intuitive and easy to use. No shame language found except BMI on profile. Results at `docs/manual-tests/phase-1-smoke-test-results.md`.
- **Phase 2: Codebase Audit** — Full audit of data layer, features/UI, and dependencies. Key findings: Hive must be replaced with drift, Supabase and Sentry should be removed, shame-based calendar colours must go, food data denormalisation is the biggest schema problem. Full audit at `docs/audit/codebase-audit.md`.
- **Phase 3: Stage 2 Roadmap** — Full 12-phase roadmap (Phases 4-15) written. CLAUDE.md updated with confirmed conventions. All 5 architectural decisions confirmed by user.

### Decisions Confirmed
- Migrate Hive → drift/SQLite
- Remove Supabase (route FDC through direct API)
- Remove Sentry (keep Logger)
- Keep BLoC architecture, modify UI content
- Simplify onboarding (defer to later phase)

### Issues Encountered
1. intl version conflict (fixed: ^0.19.0 → ^0.20.2)
2. hive_generator vs mockito analyzer conflict (fixed: migrated to hive_ce)
3. Flutter text selection crash on long-press (framework bug, not blocking)
4. Food search broken due to Supabase dummy credentials (will be fixed in Phase 4)

### Test Counts
- Existing tests: 5 files (~5% coverage)
- New tests written: 0 (Stage 1 is planning, not coding)

### Next Steps
- Phase 4: Strip Supabase, Sentry, shame-based visuals. Fix search.
- Phase 5: Database migration (Hive → drift)

## Session 2 — 2026-02-20

### Completed

**Phase 4: Strip & Clean — COMPLETE**

- **Supabase removed** — Deleted `SpFdcDataSource`, `fdc_sp/` DTO directory, and all Supabase references. Routed FDC food search through direct API (`getFDCFoodsByString` instead of `getSupabaseFDCFoodsByString`). Removed `supabase_flutter` from pubspec. Removed env vars. Fixed lingering `SPConst.fdcNutrientId` reference in `fdc_food_nutriment_dto.dart` (inlined the constant). 28 transitive dependencies removed.
- **Sentry removed** — Removed all `Sentry.captureException()` calls from 5 files (off_data_source, fdc_data_source, edit_meal_screen, meal_detail_bloc, settings_screen). Removed `SentryFlutter.init()` from main.dart. Simplified app startup to always use `runAppWithChangeNotifiers`. Removed `sentry_flutter` from pubspec and `SENTRY_DNS` from env.
- **Shame-based visuals removed** — Calendar day dots now always use `primary` color (no more red for "bad" days). Summary cards always use `secondaryContainer` colors. Removed `maxKcalDifferenceOverGoal`/`maxKcalDifferenceUnderGoal` constants and `_hasExceededMaxKcalDifferenceGoal()` method. Removed BMI overview from profile page (removed widget usage, removed BMI calculation from ProfileBloc).
- **Barcode not-found fallback** — Added "Search by name" button to scanner failure screen. When barcode is not found, user can now tap to navigate to the add meal search screen instead of being stuck in a dead end.
- **Build verified** — `flutter build ios --debug` succeeds. All 8 existing tests pass.

### Files Changed
- `lib/core/utils/locator.dart` — Removed Supabase init, SpFdcDataSource registration, Env import
- `lib/core/utils/env.dart` — Removed SENTRY_DNS, SUPABASE_PROJECT_URL, SUPABASE_PROJECT_ANON_KEY
- `lib/main.dart` — Removed Sentry init, simplified startup
- `lib/features/add_meal/data/repository/products_repository.dart` — Removed SpFdcDataSource, Supabase method
- `lib/features/add_meal/domain/usecase/search_products_usecase.dart` — Route through direct FDC API
- `lib/features/add_meal/domain/entity/meal_entity.dart` — Removed `fromSpFDCFood` factory
- `lib/features/add_meal/data/dto/fdc/fdc_food_nutriment_dto.dart` — Inlined SPConst.fdcNutrientId
- `lib/features/add_meal/data/data_sources/off_data_source.dart` — Removed Sentry calls
- `lib/features/add_meal/data/data_sources/fdc_data_source.dart` — Removed Sentry calls
- `lib/features/edit_meal/presentation/edit_meal_screen.dart` — Removed Sentry calls
- `lib/features/meal_detail/presentation/bloc/meal_detail_bloc.dart` — Removed Sentry calls
- `lib/features/settings/settings_screen.dart` — Removed Sentry import and Sentry.close()
- `lib/core/domain/entity/tracked_day_entity.dart` — Neutralised calendar/summary card colours
- `lib/features/profile/profile_page.dart` — Removed BMI overview
- `lib/features/profile/presentation/bloc/profile_bloc.dart` — Removed BMI calculation
- `lib/features/profile/presentation/bloc/profile_state.dart` — Removed userBMI field
- `lib/features/scanner/scanner_screen.dart` — Added search fallback button
- `pubspec.yaml` — Removed sentry_flutter, supabase_flutter
- `.env` — Removed Sentry and Supabase entries
- Deleted: `lib/features/add_meal/data/data_sources/sp_fdc_data_source.dart`
- Deleted: `lib/features/add_meal/data/dto/fdc_sp/` (entire directory)

### Issues Encountered
1. `fdc_food_nutriment_dto.dart` imported deleted `sp_const.dart` for a JSON key name — fixed by inlining the `'nutrient_id'` string constant

### Test Counts
- Existing tests: 8 (all pass)
- New tests written: 0 (Phase 4 is removal, not new features)

### Next Steps
- Phase 5: Database migration (Hive → drift)
- Deploy to device for manual verification of food search and scanner fallback

## Session 3 — 2026-02-21

### Completed

**Phase 5: Database Migration (Hive → drift) — COMPLETE**

- **Drift schema** — 6 tables: `food_items`, `log_entries`, `daily_stats`, `config` (key-value), `user_profile`, `user_activities`. FTS5 virtual table on `food_items(name, brand)` for search. Indexes on timestamp, meal_slot, barcode, date.
- **DAOs** — `FoodItemDao`, `LogEntryDao`, `DailyStatsDao`, `ConfigDao`, `UserProfileDao`, `UserActivityDao`. All with typed queries (no full scans). `DailyStatsDao` uses atomic SQL updates (`SET col = col + ?`) instead of read-modify-write.
- **Entity factory methods** — Added `fromLogEntry`, `fromFoodItem`, `fromDailyStatRow`, `fromProfileRow`, `fromActivityRow` to existing entity classes. Old `fromDBO` factories retained for migration/import compatibility.
- **Hive → drift migrator** — One-time migration reads all 5 Hive boxes, extracts embedded `MealDBO` into standalone `food_items` rows (deduplicated by barcode or UUID v5 hash of name+brand), maps `IntakeDBO` to `log_entries` with snapshot values, converts locale-dependent date keys to ISO 8601.
- **Repositories rewritten** — `IntakeRepository` (takes `LogEntryDao` + `FoodItemDao`, upserts food item before inserting log entry), `TrackedDayRepository`, `ConfigRepository` (key-value), `UserRepository`, `UserActivityRepository`. All keep same public API.
- **locator.dart updated** — Creates `AppDatabase`, DAOs, runs migrator, registers repositories with DAOs. Removed all Hive data source registrations. Fixed `main.dart` to use `UserRepository` instead of removed `UserDataSource`.
- **Export/import updated** — `ExportDataUsecase` and `ImportDataUsecase` query drift but produce/consume the same JSON shape as before (backward compatible).
- **Test updated** — `intake_repository_test.dart` rewritten to use in-memory drift database. All 8 tests pass.

### Files Created
- `lib/core/data/drift/tables/` — 6 table definition files
- `lib/core/data/drift/daos/` — 6 DAO files
- `lib/core/data/drift/app_database.dart` — Central database class
- `lib/core/data/drift/migration/hive_to_drift_migrator.dart` — One-time migration
- `docs/manual-tests/phase-5-db-migration.md` — Manual test brief

### Files Modified
- `pubspec.yaml` — Added drift, sqlite3_flutter_libs, path, drift_dev
- `lib/main.dart` — Changed `UserDataSource` → `UserRepository`
- `lib/core/utils/locator.dart` — Full rewrite for drift wiring
- `lib/core/data/repository/` — All 5 repositories rewritten for drift DAOs
- `lib/core/domain/entity/` — 3 entity files got drift factory methods
- `lib/features/add_meal/domain/entity/` — 2 entity files got drift factory methods
- `lib/features/settings/domain/usecase/` — Export + import rewritten for drift
- `test/unit_test/intake_repository_test.dart` — Rewritten for in-memory drift

### Manual Test Results
- App launch & migration: PASS
- Log a food: PASS (search slow but working)
- View diary/calendar: PASS
- Delete a food: PASS (totals update; calendar dot persists — noted for Phase 7)
- Log an activity: PASS
- Delete an activity: PASS
- Edit food amount: PASS
- Settings persist: PASS
- Export data: PASS
- Recently used foods: PASS

### Issues Found (Not Phase 5 — Noted for Later)
1. Calendar dot doesn't clear when all items deleted (→ Phase 7.7)
2. Home page long-press delete UX poor vs diary confirmation dialog (→ Phase 7.8)
3. Food search occasionally throws null cast errors in FDC/OFF parsers (pre-existing)
4. Image full-screen tap can freeze app (pre-existing)

### Test Counts
- Existing tests: 8 (all pass)
- New tests written: 0 (intake_repository_test rewritten, not new)

### Next Steps
- Phase 6: Core Domain — pure calculation engine

## Session 5 — 2026-02-22

### Completed

**Phase 7: Today Dashboard + Allowance UI — COMPLETE**

- **Date row** — "Today" left-aligned, formatted date right-aligned, subdued style
- **Calorie circle overage** — Shows "X over target" in neutral styling when over goal (no red, no clamping to 0)
- **Allowance breakdown** — Row of three columns below circle: base / +earned / eaten. Replaces old "supplied"/"burned" flanking columns.
- **Weekly context line** — "This week: X kcal under/over target" from WeeklyCalc.aggregate(), hidden when no data
- **Horizontal macro bars** — Replaced three small circular gauges with LinearPercentIndicator bars (label | bar | intake/goal g)
- **Calendar dot cleanup** — Added deleteByDate() to DailyStatsDao, deleteDayIfEmpty() to TrackedDayRepository, called after intake/activity deletes
- **HomeBloc enriched** — Computes totalKcalBase (no exercise), totalKcalEarned (allowance - base), weeklyRemaining via GetTrackedDayUsecase + WeeklyCalc
- **L10n** — Added 6 new strings (base, earned, eaten, over target, weekly under/over) to all 3 ARB files

### Files Changed
- `lib/features/home/presentation/widgets/dashboard_widget.dart` — Full layout rewrite
- `lib/features/home/presentation/widgets/macro_nutriments_widget.dart` — Circular → linear bars
- `lib/features/home/presentation/bloc/home_state.dart` — Added totalKcalBase, totalKcalEarned, weeklyRemaining
- `lib/features/home/presentation/bloc/home_bloc.dart` — Computes new fields, calls deleteDayIfEmpty
- `lib/features/home/home_page.dart` — Passes new fields to DashboardWidget
- `lib/core/data/drift/daos/daily_stats_dao.dart` — Added deleteByDate()
- `lib/core/data/repository/tracked_day_repository.dart` — Added deleteDayIfEmpty()
- `lib/core/domain/usecase/add_tracked_day_usecase.dart` — Added deleteDayIfEmpty()
- `lib/core/utils/locator.dart` — Added GetTrackedDayUsecase to HomeBloc
- `lib/l10n/intl_en.arb`, `intl_de.arb`, `intl_tr.arb` — 6 new strings each

### Files Created
- `test/widget_test/dashboard_widget_test.dart` — 7 widget tests
- `test/widget_test/macro_nutriments_widget_test.dart` — 3 widget tests
- `docs/manual-tests/phase-7-today-dashboard.md` — Manual test brief (9 tests)

### Files Removed
- `test/widget_test/widget_test.dart` — Replaced by dashboard_widget_test.dart

### Test Counts
- Existing tests: 66 (all pass)
- New tests written: 10 (7 dashboard + 3 macro bars)
- Total: 76

### Next Steps
- Phase 8: Friction Foundations

## Session 6 — 2026-02-22

### Completed

**Phase 8: Friction Foundations — COMPLETE**

- **Portion memory (8.1 + 8.3)** — MealDetailScreen now defaults to lastUsedGrams instead of 100g when a food has been logged before. Falls back to serving (1), imperial (1 oz), or metric (100g).
- **Increment buttons (8.2)** — +10g, +50g, +100g chip buttons in MealDetailBottomSheet between quantity input and Add button. Shows +0.5 srv / +1 srv when serving unit is selected.
- **Favourites (8.4)** — `favourite` boolean column added to food_items table (schema v2, migration from v1). Star icon on MealDetailScreen app bar and on MealItemCard in Recently tab. Favourites section at top of Recently tab, separated from recents by header and divider. Toggle via RecentMealBloc → GetIntakeUsecase → IntakeRepository → FoodItemDao.
- **Improved recents with one-tap re-log (8.5)** — MealItemCard shows "last: Xg" subtitle when lastUsedGrams available. Filled circle add icon triggers instant logging at last-used amount, bypassing detail screen.
- **Inline editing improvements (8.6)** — EditDialog now has +10g/+50g/+100g increment chips, live kcal estimate that updates as amount changes, and auto-selected text for easy replacement.
- **Swipe to delete with undo (8.7)** — IntakeCard wrapped in Dismissible (swipe down). Neutral background with trash icon. Undo SnackBar re-adds the intake on tap.
- **Meal slot auto-suggestion (8.9)** — AddMealScreenArguments.mealType now nullable. When null, uses MealSlotCalc.suggestSlot() from Phase 6 (breakfast 6-10, lunch 10-14, dinner 17-21, else snack).
- **Deferred (8.8)** — Draft state persistence deferred to Phase 15 (Polish).

### Files Modified
- `lib/core/data/drift/tables/food_items.dart` — Added favourite column
- `lib/core/data/drift/app_database.dart` — Schema v2, onUpgrade migration
- `lib/core/data/drift/daos/food_item_dao.dart` — toggleFavourite, getFavourites, getRecentlyUsed
- `lib/core/data/repository/intake_repository.dart` — toggleFavourite, getFavouriteMeals
- `lib/core/domain/usecase/get_intake_usecase.dart` — getFavouriteMeals, toggleFavourite
- `lib/features/add_meal/domain/entity/meal_entity.dart` — lastUsedGrams, favourite fields, copyWithFavourite
- `lib/features/meal_detail/meal_detail_screen.dart` — Portion memory defaults, star icon
- `lib/features/meal_detail/presentation/widgets/meal_detail_bottom_sheet.dart` — Increment chips
- `lib/features/add_meal/presentation/add_meal_screen.dart` — Favourites section, quick-add, auto-suggestion
- `lib/features/add_meal/presentation/bloc/recent_meal_bloc.dart` — Favourites loading, ToggleFavouriteEvent
- `lib/features/add_meal/presentation/bloc/recent_meal_event.dart` — ToggleFavouriteEvent
- `lib/features/add_meal/presentation/bloc/recent_meal_state.dart` — favouriteMeals list
- `lib/features/add_meal/presentation/widgets/meal_item_card.dart` — Star icon, quick-add, last-used subtitle
- `lib/core/presentation/widgets/intake_card.dart` — Dismissible wrapper, onItemDismissed callback
- `lib/core/presentation/widgets/edit_dialog.dart` — Increment chips, live kcal, auto-select
- `lib/features/home/presentation/widgets/intake_vertical_list.dart` — Dismiss callback with undo snackbar
- `lib/l10n/intl_en.arb`, `intl_de.arb`, `intl_tr.arb` — favouritesLabel, undoLabel

### Files Created
- `test/unit_test/food_item_dao_test.dart` — 7 tests (favourite toggle, getFavourites, updateLastUsed, getRecentlyUsed)
- `test/unit_test/meal_entity_test.dart` — 5 tests (lastUsedGrams, favourite, copyWithFavourite, empty)
- `docs/manual-tests/phase-8-friction-foundations.md` — Manual test brief (9 test cases)

### Test Counts
- Existing tests: 76 (all pass)
- New tests written: 12 (7 DAO + 5 entity)
- Total: 88

### Next Steps
- Phase 9: Apple Health integration

## Session 7 — 2026-02-22

### Completed

**Phase 9: Apple Health Integration — COMPLETE**

- **health package** — Added `health: ^13.3.1` to pubspec. Configured `Info.plist` with `NSHealthShareUsageDescription` and `NSHealthUpdateUsageDescription`. Created `Runner.entitlements` with HealthKit capability.
- **Schema migration v2→v3** — Added `active_calories_burned` (REAL, default 0.0) and `active_calories_updated_at` (INTEGER, nullable) columns to `daily_stats`. Migration in `onUpgrade`.
- **HealthDataSource** — Wraps `health` package. `requestPermission()`, `hasPermission()`, `getActiveCaloriesToday()`. Handles iOS quirk where `hasPermissions` for READ always returns `null` (treated as true). Calls `Health().configure()` before first use.
- **HealthRepository** — `fetchAndCacheActiveCalories()` reads from HealthKit and writes to `daily_stats`. `getCachedActiveCalories()` reads from DB. Coordinates `HealthDataSource` and `DailyStatsDao`.
- **GetHealthUsecase** — `getActiveCalories()`, `getEarnedCalories()` (applies exercise multiplier), permission delegation.
- **GetKcalGoalUsecase updated** — Now takes `HealthRepository`. When no explicit `totalKcalActivitiesParam` passed: checks HealthKit permission → if yes, uses HealthKit active calories; if no, falls back to manual `UserActivity` sum.
- **HomeBloc updated** — On first `LoadItemsEvent`, checks `hasAskedHealthPermission` config flag. If not asked, requests permission once. Passes `activeCaloriesToday`, `activeCaloriesUpdatedAt`, `healthKitConnected` to state.
- **Foreground refresh** — `home_page.dart` now always fires `LoadItemsEvent` on app resume (was day-change only), so HealthKit data refreshes when user returns to app.
- **Dashboard** — Shows "Active calories: X kcal" line when HealthKit connected and active > 0. Shows "updated X min ago" when cache is stale (> 15 min).
- **Permission flow** — One-shot: asks once, stores `hasAskedHealthPermission` in config. If denied, falls back silently to manual activities. No nag dialogs.
- **L10n** — Added 5 new strings (activeCaloriesLabel, lastUpdatedLabel, healthPermissionTitle, healthPermissionBody, healthDeniedNote) to en/de/tr ARB files.

### Files Created
- `lib/core/data/data_source/health_data_source.dart`
- `lib/core/data/repository/health_repository.dart`
- `lib/core/domain/usecase/get_health_usecase.dart`
- `ios/Runner/Runner.entitlements`
- `test/unit_test/health_repository_test.dart` — 4 tests (write+read, missing date, overwrite, defaults)
- `test/unit_test/get_kcal_goal_usecase_test.dart` — 5 tests (HealthKit path, manual fallback, explicit override, zero, earned delta)
- `docs/manual-tests/phase-9-apple-health.md` — 7 test cases

### Files Modified
- `pubspec.yaml` — Added health ^13.3.1
- `ios/Runner/Info.plist` — Health usage descriptions
- `lib/core/data/drift/tables/daily_stats.dart` — 2 new columns
- `lib/core/data/drift/app_database.dart` — Schema v3, migration
- `lib/core/data/drift/daos/daily_stats_dao.dart` — updateActiveCalories, getActiveCalories
- `lib/core/data/repository/config_repository.dart` — hasAskedHealthPermission get/set
- `lib/core/domain/usecase/get_kcal_goal_usecase.dart` — HealthRepository dependency, HealthKit preference
- `lib/core/utils/locator.dart` — Health data source, repository, usecase registrations; HomeBloc updated
- `lib/features/home/presentation/bloc/home_bloc.dart` — HealthKit permission + active calories
- `lib/features/home/presentation/bloc/home_state.dart` — 3 new fields
- `lib/features/home/presentation/widgets/dashboard_widget.dart` — Active calories line
- `lib/features/home/home_page.dart` — New params, always-refresh on resume
- `lib/l10n/intl_en.arb`, `intl_de.arb`, `intl_tr.arb` — 5 new strings each
- `test/unit_test/calorie_goal_calc_test.dart` — 4 new tests for Phase 9 edge cases

### Test Counts
- Existing tests: 88 (all pass)
- New tests written: 13 (4 calorie calc + 4 health repo + 5 kcal goal)
- Total: 101

### Next Steps
- Manual test on device (HealthKit permission, foreground refresh, multiplier changes)
- Phase 10: Quick-Add
