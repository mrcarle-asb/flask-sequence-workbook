---
name: "Optional: The AMB Mobilitat API"
index: 4
---

# Optional: The AMB Mobilitat API

## The scope question

The TMB API covers transit services that operate **within Barcelona** — TMB's own buses and metro lines. But Barcelona's transit network doesn't stop at the city border. Dozens of bus routes connect Barcelona to surrounding towns, operated by companies other than TMB.

If you live outside Barcelona proper — or if your stop is served by a non-TMB operator — the TMB API won't have your bus. This matters for us: **ASB is in Esplugues de Llobregat**, and many of the bus routes near school are operated by [Soler i Sauret](http://www.solerisauret.com/), not TMB.

That's where the **AMB Mobilitat API** comes in. AMB (Àrea Metropolitana de Barcelona) is the umbrella organization that unifies transit information from all the operators serving the metropolitan area. It covers TMB's routes *and* the inter-city routes from companies like Soler i Sauret, Mohn, Tusgsal, and others.

| API | Scope | Our school's buses? |
|-----|-------|---------------------|
| **TMB** | Barcelona city (TMB buses + metro) | Some — only the TMB lines that reach Esplugues |
| **AMB Mobilitat** | Full metropolitan area (all operators) | Yes — including Soler i Sauret routes near school |

---

## Key differences from the TMB API

The AMB API works differently from TMB in a few important ways:

### Authentication

TMB uses query parameters (`?app_id=...&app_key=...`). AMB uses a **request header**:

```python
headers = {"x-api-key": "your_amb_api_key"}
response = requests.get(url, headers=headers)
```

This is a common pattern in APIs — credentials go in the headers instead of the URL. You'll need a separate API key for AMB (your teacher will provide it).

### Base URL

```
https://api.ambmobilitat.cat/v1
```

### Arrival time format

TMB returns arrival times as human-readable strings (`"3 min"`). AMB returns **`arrivalTime` in seconds** — you need to convert:

```python
minutes = arrival["arrivalTime"] // 60
```

### Response structure

TMB nests arrivals inside `response.json()["data"]["ibus"]`. AMB returns arrivals in a `times[]` array with different field names:

::::tabs{id="api-compare"}

:::tab{title="TMB response"}
```json
{
  "data": {
    "ibus": [
      {"line": "H6", "text-ca": "3 min", "destination": "Feixa Llarga"}
    ]
  }
}
```
Access: `response.json()["data"]["ibus"]`

Fields: `line`, `text-ca`, `destination`
:::

:::tab{title="AMB response"}
```json
{
  "times": [
    {"lineCode": "EP1", "destination": "Cornellà", "arrivalTime": 180}
  ]
}
```
Access: `response.json()["times"]`

Fields: `lineCode`, `destination`, `arrivalTime` (in seconds)
:::

::::

---

## Useful AMB endpoints

You don't need all of these — pick the ones relevant to what you're building.

| Endpoint | What it does |
|----------|-------------|
| `GET /stops/search/byName?name=...` | Find stops by name — good for discovering stop IDs |
| `GET /stops/{id}/realtimes` | Live arrivals for one stop |
| `GET /stops/search/realtimes/byIds?idStops=...` | Live arrivals for multiple stops in one call (more efficient than looping) |
| `GET /lines` | All transit lines, paginated |
| `GET /lines/{id}/stops` | All stops on a line, in order |
| `GET /companies` | All transit operators in the AMB network |
| `GET /companies/34/lines` | All lines operated by Soler i Sauret (company ID 34) |

---

## The challenge

Extend your app to show arrivals from **both** APIs — TMB for Barcelona city stops and AMB for metropolitan stops. This means:

- A second set of credentials (AMB API key) in your `.env` file
- A second `requests.get()` call with a header instead of query params
- Parsing a different JSON structure
- Converting `arrivalTime` from seconds to a readable format

This is genuinely harder than the base assignment. You're dealing with two APIs that return the same *kind* of data in different *shapes*. Getting them to display in the same template means normalizing the data — making both APIs' output look the same before passing it to Jinja2.

:::alert{info}
This is optional. If you complete the base TMB assignment and want a real challenge, this is it. If not, the TMB-only version is complete and sufficient.
:::
