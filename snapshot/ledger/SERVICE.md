# FoodTracker

Provide a JSON export of foods, recipes, logs, and cached daily stats for backup or downstream analysis.

## What it can do

Provide a JSON export of foods, recipes, logs, and cached daily stats for backup or downstream analysis.

## How to call it

<!-- svcmap:generated:implemented:start -->
### LLM food capture
```console
$ foodtracker_cli --recipe-text 2 eggs --api-key <key>
```
<!-- svcmap:generated:implemented:end -->

## Files this service reads or writes

_See the structured authority for verified file contracts._

## Access and prerequisites

_See each implemented call record._

## Planned

- Export nutrition data: ExportDataUsecase.exportData is bound to the Flutter runtime — it reads the Drift DB and its final save step uses the file_picker Flutter plugin, so it cannot run under plain Dart. Would need a Flutter-context or an extracted DB-only path.
- Pull recipe nutrition: MealieSyncService.syncAllRecipes upserts into the local Drift store via the Flutter data layer (get_it DI + Drift), so it cannot run as a plain-Dart CLI. Only the HTTP fetch is pure Dart; the persistence is Flutter-bound.
- Scan packaged foods: SearchProductByBarcodeUseCase delegates through ProductsRepository/OFFDataSource whose chain imports package:flutter/material (via lib/core/data data sources), so it cannot run under plain Dart. HTTP-only extraction would need decoupling from the Flutter data layer.

```svcmap-card-json
{
  "implemented": [
    {
      "call": {
        "argv": [
          "foodtracker_cli",
          "--recipe-text",
          "2 eggs",
          "--api-key",
          "<key>"
        ],
        "result": "calls ClaudeRecipeDataSource.parseRecipeFromText, prints parsed recipe JSON"
      },
      "kind": "callable",
      "label": "LLM food capture",
      "surface_ref": {
        "command": "foodtracker_cli",
        "record_id": "FoodTracker/cli/foodtracker_cli",
        "surface": "cli"
      }
    }
  ],
  "planned": [
    {
      "kind": "callable",
      "label": "Export nutrition data",
      "owner_unit": "FoodTracker/wave3-detector-gap",
      "plan_path": "/Users/aidan/.claude/skills/deep-plan/runs/servicemap-program/wave1-findings.md",
      "reason": "ExportDataUsecase.exportData is bound to the Flutter runtime — it reads the Drift DB and its final save step uses the file_picker Flutter plugin, so it cannot run under plain Dart. Would need a Flutter-context or an extracted DB-only path."
    },
    {
      "kind": "callable",
      "label": "Pull recipe nutrition",
      "owner_unit": "FoodTracker/wave3-detector-gap",
      "plan_path": "/Users/aidan/.claude/skills/deep-plan/runs/servicemap-program/wave1-findings.md",
      "reason": "MealieSyncService.syncAllRecipes upserts into the local Drift store via the Flutter data layer (get_it DI + Drift), so it cannot run as a plain-Dart CLI. Only the HTTP fetch is pure Dart; the persistence is Flutter-bound."
    },
    {
      "kind": "callable",
      "label": "Scan packaged foods",
      "owner_unit": "FoodTracker/wave3-detector-gap",
      "plan_path": "/Users/aidan/.claude/skills/deep-plan/runs/servicemap-program/wave1-findings.md",
      "reason": "SearchProductByBarcodeUseCase delegates through ProductsRepository/OFFDataSource whose chain imports package:flutter/material (via lib/core/data data sources), so it cannot run under plain Dart. HTTP-only extraction would need decoupling from the Flutter data layer."
    }
  ],
  "project": "FoodTracker",
  "schema_version": 1,
  "source": {
    "decisions_file": "/Users/aidan/Dev/FoodTracker/ledger/decisions.json",
    "fingerprint": "aef4367da588eb6f849d3947c64568a3d77bc7026809b251eb37527adf24d946",
    "project": "FoodTracker"
  },
  "summary": "Provide a JSON export of foods, recipes, logs, and cached daily stats for backup or downstream analysis."
}
```
