---
name: weather
description: Check the current weather or forecast for a specified location using the Open-Meteo geocoding and forecast APIs. Use when asked about weather, temperature, precipitation, wind, or a forecast.
---

# Weather via Open-Meteo

Use Open-Meteo's public APIs to answer weather requests. No API key is required.

## Location

1. Use the location provided by the user. If it is missing or ambiguous, ask for a city, locality, or coordinates. Do not infer the user's location from their machine, IP address, or other sources.
2. Resolve a named location with the geocoding endpoint:
   ```
   https://geocoding-api.open-meteo.com/v1/search?name=<URL-encoded location>&count=1&language=<language>&format=json
   ```
   Use the user's language for `<language>` when possible, otherwise use `en`.
3. If no result is returned, state that the location could not be found and ask the user to clarify it.
4. If multiple places are plausibly intended, present the alternatives and ask which one they mean rather than choosing arbitrarily.

## Current weather

Request current conditions from:
```
https://api.open-meteo.com/v1/forecast?latitude=<latitude>&longitude=<longitude>&current=temperature_2m,apparent_temperature,relative_humidity_2m,precipitation,rain,showers,snowfall,weather_code,cloud_cover,wind_speed_10m,wind_direction_10m&timezone=auto
```

Report the resolved location, observation time and local time zone, temperature and feels-like temperature, conditions decoded from `weather_code`, precipitation when nonzero, and wind speed/direction. Include humidity or cloud cover when useful.

## Forecasts

For a forecast, include the needed daily fields:
```
&daily=weather_code,temperature_2m_max,temperature_2m_min,precipitation_sum,precipitation_probability_max,wind_speed_10m_max&forecast_days=<number>&timezone=auto
```

- Use the requested date range. If no range is given, provide today and the next two days.
- State temperatures in °C by default; use `temperature_unit=fahrenheit` only when the user requests Fahrenheit.
- Keep the response concise and organize multi-day results in a readable list or table.

## Weather codes

Decode WMO weather codes in the response:

| Codes | Description |
| --- | --- |
| 0 | Clear sky |
| 1–3 | Mainly clear to overcast |
| 45, 48 | Fog |
| 51, 53, 55 | Drizzle |
| 56, 57 | Freezing drizzle |
| 61, 63, 65 | Rain |
| 66, 67 | Freezing rain |
| 71, 73, 75, 77 | Snow |
| 80, 81, 82 | Rain showers |
| 85, 86 | Snow showers |
| 95 | Thunderstorm |
| 96, 99 | Thunderstorm with hail |

## Reliability

- Mention that Open-Meteo data is a forecast/model estimate when relevant; do not present it as an official meteorological warning.
- For requests about warnings, advisories, or severe conditions, recommend checking the applicable official meteorological service.
- If an API request fails, communicate the failure briefly instead of inventing conditions.
