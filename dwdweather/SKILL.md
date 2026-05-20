---
name: dwdweather
description: Use DWD/BrightSky weather data for German current conditions, forecasts, history, alerts, stations, and summaries.
version: 1.0.0
author: hran
license: MIT
platforms: [macos, linux]
metadata:
  hermes:
    tags: [weather, dwd, germany, cli]
    category: weather
---

# dwdweather

Use this skill when the user asks for weather information for a location in Germany, especially current conditions, forecasts, historical observations, DWD weather warnings, nearby DWD stations, or a compact weather summary. Also use it when the user mentions DWD, Deutscher Wetterdienst, BrightSky, or `dwdweather`.

The skill wraps the bundled CLI:

```bash
${HERMES_SKILL_DIR}/bin/dwdweather
```

The CLI uses BrightSky data derived from Deutscher Wetterdienst data. It requires internet access but no API key, credentials, or required environment variables.

**Output format:** All CLI calls use `--output toon`. The CLI emits TOON v3.0 format — the same structure as JSON but typically 20–60% smaller, wrapped in a ` ```toon ` code fence. Errors are also emitted as TOON when this format is selected.

## Default Workflow

For broad weather questions such as "Wie wird das Wetter in Berlin?" or "weather in Hamburg", call:

```bash
${HERMES_SKILL_DIR}/bin/dwdweather summary Berlin --output toon
```

Default to `--output toon` for all CLI calls. Parse the TOON content and answer the user in concise natural language. Only show raw output when the user explicitly asks for structured data, debugging details, or machine-readable output.

Use the CLI default timezone, `Europe/Berlin`, unless the user asks for another IANA timezone. In that case pass `--tz`, for example:

```bash
${HERMES_SKILL_DIR}/bin/dwdweather forecast Berlin --days 2 --tz UTC --output toon
```

`DWDWEATHER_TZ` can optionally set the default timezone outside the skill, but it is not required.

## Command Recipes

Use `summary` for general weather requests. It returns current conditions, a daily outlook, and active alerts when available:

```bash
${HERMES_SKILL_DIR}/bin/dwdweather summary LOCATION --days 5 --output toon
```

Use `current` for current observed conditions:

```bash
${HERMES_SKILL_DIR}/bin/dwdweather current LOCATION --output toon
```

Use `forecast` for forecasts. `--days` must be from `1` to `10`; the default is `3`. Add `--daily` when the user wants daily aggregates instead of hourly records:

```bash
${HERMES_SKILL_DIR}/bin/dwdweather forecast LOCATION --days 3 --output toon
${HERMES_SKILL_DIR}/bin/dwdweather forecast LOCATION --days 7 --daily --output toon
```

Use `history` for past observations. `--date YYYY-MM-DD` is required. `--end-date YYYY-MM-DD` is optional and inclusive. Date ranges must not exceed 366 days. Add `--daily` for daily aggregates:

```bash
${HERMES_SKILL_DIR}/bin/dwdweather history LOCATION --date 2026-05-01 --output toon
${HERMES_SKILL_DIR}/bin/dwdweather history LOCATION --date 2026-05-01 --end-date 2026-05-07 --daily --output toon
```

Use `alerts` for active DWD weather warnings:

```bash
${HERMES_SKILL_DIR}/bin/dwdweather alerts LOCATION --output toon
```

Use `stations` to find nearby DWD observation stations. `--radius` must be from `1` to `1000` km; the default is `50`. `--limit` must be from `1` to `100`; the default is `15`:

```bash
${HERMES_SKILL_DIR}/bin/dwdweather stations LOCATION --radius 50 --limit 15 --output toon
```

## Locations

Pass the user's German location name directly to the CLI. Multi-word locations may be passed as separate arguments or quoted as one argument:

```bash
${HERMES_SKILL_DIR}/bin/dwdweather summary Freiburg im Breisgau --output toon
${HERMES_SKILL_DIR}/bin/dwdweather summary "Freiburg im Breisgau" --output toon
```

Do not use this skill for locations outside Germany. If the user asks for a non-German location, explain that this skill is Germany-only because DWD warnings and BrightSky/DWD geocoding are Germany-focused.

## Answering Rules

- Convert JSON results into a concise natural-language answer.
- Report only values present in the JSON. Do not invent missing weather values.
- Include active warnings when present, especially severe or extreme alerts.
- Mention the observation time and station/source when it is relevant to current or historical data.
- For forecasts, summarize the requested period instead of listing every hourly record unless the user asks for detail.
- For history, respect the requested dates and mention when no data is available.

## Errors

Handled errors are returned as TOON:

````
```toon
error:
  code: NO_DATA
  message: No forecast data available for this location.
  exit_code: 4
```
````

When an error is returned, read the `message` and explain it plainly. Common cases:

- `exit_code` 2: invalid CLI usage, date format, range, option value, or timezone.
- `exit_code` 3: location not found, usually because the location is misspelled or outside Germany.
- `exit_code` 4: valid input but no weather, station, or history data is available.
- `exit_code` 1: network, BrightSky API, or general runtime failure.

For invalid timezone errors, ask for an IANA timezone such as `Europe/Berlin` or `UTC`. For missing historical data, suggest checking a smaller date range or nearby station coverage with `stations`.
