# FoodTracker: what it is now

**Date:** 2026-11-01

FoodTracker is now the food part of Aidan and Emily's household system, rather than a separate tracker they need to maintain. They use Mantel to record food, see a day and week, and plan meals. Mealie is the shared recipe library. In plain terms: Mantel is the household app and server; Mealie keeps recipes; the older FoodTracker app is the earlier phone tracker and its plan.

The live food log is stored on the Mac mini in Mantel's SQLite file, `app/mantel.db`. Mantel's `app/server.py` serves the household food, entry, day and week routes. The checked database has two household profiles and existing food, entry and sync records; no food entries or other personal data were read. FoodTracker's older Flutter diary has a one-way Mantel import, so it can mirror Mantel meals locally, but it is not the household source of truth.

The old Vision Statement, Technical Brief and ROADMAP describe a standalone iPhone calorie-and-macro tracker. They promise a fast barcode flow, local food diary, Apple Health allowance, quick estimates, label-photo reading, recipe building, reminders, data export/import, detailed settings and a final kindness/performance review. Some of that was built in the older app, according to its ledger. The documents do not describe the present split: Mantel now holds and serves the household log, and Mealie replaces the planned recipe builder. They should be treated as history, not a current delivery plan.

## Open items

1. Agree the household direction: whether Mantel, Mealie and the old FoodTracker client are the intended long-term shape.
2. Decide whether the old FoodTracker app should remain a Mantel mirror, be updated, or be retired.
3. Define the live ownership and backup/export path for the Mantel food log.
4. Check the live household food flow on both phones, including day/week views and corrections.
5. Decide which unfinished old-app features still matter: Apple Health background refresh, reminders, targets, units, and import/export.
6. Do one final plain-language, speed and non-judgemental-design review of the experience people actually use.
