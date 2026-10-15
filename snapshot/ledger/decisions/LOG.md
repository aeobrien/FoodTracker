# Decision Log

Decisions are recorded when there was a genuine choice between alternatives. Routine implementation details do not need entries.

| # | Date | Decision | Alternatives | Rationale | Impacts |
|---|------|----------|-------------|-----------|---------|
| 1 | 2026-02-20 | Exercise multiplier default 75% | 100%, 50% | Apple Watch calorie estimates have ~25-30% mean error | Core domain, settings |
| 2 | 2026-02-20 | Fork and diverge from OpenNutriTracker | Contribute upstream, build from scratch | Personal tool, freedom to restructure | All |
| 3 | 2026-02-20 | Macro targets scale proportionally (with per-macro override) | Fixed macros only, proportional only | Flexible dieting strategies (e.g., fixed protein on keto) | Core domain, settings |
| 4 | 2026-02-20 | User enters own Claude API key | Bundle key, proxy server | Can't bundle key in GitHub-hosted code | Settings, Claude API integration |
| 5 | 2026-02-20 | Day boundary configurable, default 2 AM | Fixed midnight, no boundary | Night-owl eating patterns | Core domain, database |
| 6 | 2026-02-20 | Strip EXIF from photos, no privacy notice | Privacy notice, don't strip | Personal-use app, good practice | Claude API integration |
| 7 | 2026-02-20 | "Earned calories" language retained | "Exercise bonus", "Activity credit" | User-tested, works for motivational context | Today dashboard |
| 8 | 2026-02-20 | Two-stage roadmap | Single roadmap | De-risks building on unfamiliar codebase | All |
| 9 | 2026-02-20 | Migrate Hive to drift/SQLite | Keep Hive, use Isar | Hive lacks relational queries, indexes, FTS. Every planned feature requires workarounds. | Database |
| 10 | 2026-02-20 | Remove Supabase | Keep for search | Used for one FDC search path. Direct API exists. Conflicts with local-first. | Database, search |
| 11 | 2026-02-20 | Remove Sentry | Keep for crash reporting | No DSN configured. All errors already logged via Logger. | Core |
| 12 | 2026-02-20 | Keep BLoC architecture, modify UI content | Rewrite state management | Architecture is solid. UI needs content changes, not structural rebuilds. | All UI |
