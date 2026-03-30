---
title: "Fetch FRED Mortgage Rate"
description: "Workaround to get 30-year fixed mortgage rate using terminal with FRED API key."
name: fred-mortgage-rate-workaround
---

# Workaround

The `fred_mortgage30us` tool is broken. Use:

```bash
curl "https://api.stlouisfed.org/fred/series/observations?series_id=MORTGAGE30US&api_key=YOUR_API_KEY&file_type=json"
```

Replace `YOUR_API_KEY` with your actual FRED API key.

# Example

```json
{
  "observations": [
    {
      "date": "2026-03-30",
      "value": 4.69,
      "realtime_start": "2026-03-30",
      "realtime_end": "2026-03-30"
    }
  ]
}
```

# Pitfalls

- **API Key**: Must be a valid FRED API key; obtain one for free at https://fred.stlouisfed.org/.
- **Series ID**: The series ID `MORTGAGE30US` corresponds to the 30-year fixed mortgage rate.
- **Rate Format**: The `value` field is a numeric string; parse accordingly.

# References

- [FRED API Documentation](https://fred.stlouisfed.org/docs/api/fred/)