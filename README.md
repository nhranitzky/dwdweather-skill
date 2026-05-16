# DWDWeather Skill for Hermes

Use this skill to get German weather data from Deutscher Wetterdienst sources through the bundled `dwdweather` CLI. It supports current conditions, forecasts, historical observations, active DWD warnings, nearby DWD stations, and compact weather summaries for German locations.

The skill uses BrightSky/DWD data and does not require an API key.

## Installation

### Managed skill directory (via Hermes CLI)

```bash
hermes skills install nhranitzky/dwdweather-skill/dwdweather
```

> **Note:** The installation may be blocked by default for community-source skills:
> ```
> Installation blocked: Blocked (community source + caution verdict, 2 findings).
> Use --force to override.
> ```
> This skill does not require sensitive environment variables or credentials.
> Review the source code, then install with:

```bash
hermes skills install nhranitzky/dwdweather-skill/dwdweather --force
```

### Custom directory (skills.external_dirs)

```bash
git clone https://github.com/nhranitzky/dwdweather-skill.git
 
cp -R dwdweather-cli/dwdweather /path/to/skills
```

Then add `/path/to/skills` to `skills.external_dirs` so Hermes can discover `/path/to/skills/dwdweather/`.

## Configuration

No required configuration is needed.

Optionally set `DWDWEATHER_TZ` to change the default timezone used by the CLI when `--tz` is not passed:

```dotenv
DWDWEATHER_TZ=Europe/Berlin
```

The skill defaults to `Europe/Berlin` and requires internet access for BrightSky/DWD weather and geocoding requests.

## Usage

See [README.md](dwdweather/scripts/README.md)

## License

MIT

## Creation

Created with the help of an AI coding tool, then human reviewed.
