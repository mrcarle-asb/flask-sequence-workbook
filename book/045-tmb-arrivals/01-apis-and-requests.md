---
name: APIs, Requests, and JSON
index: 1
---

# APIs, Requests, and JSON

## What's an API?

An :t[API] is a URL that returns data instead of a web page. When you visit `tmb.cat` in a browser, you get HTML with colors and buttons. When your Python code calls the TMB API, it gets back structured data — a list of buses and when they arrive.

Your Flask app is about to play two roles at once:

1. **Server** — it receives requests from the browser and returns HTML (this is what Flask has always done)
2. **Client** — it sends requests to the TMB API and receives :t[JSON] data

The browser never talks to TMB directly. Your app fetches the data, then renders it into a template for the browser.

---

## The `requests` library

To make HTTP calls from Python, you use the `requests` library:

```python
import requests

url = "https://api.tmb.cat/v1/ibus/stops/556"
response = requests.get(url, params={"app_id": TMB_APP_ID, "app_key": TMB_APP_KEY})
```

`requests.get()` sends a GET request — the same kind your browser sends when you visit a URL. The `params` dict adds query parameters to the URL (like `?app_id=xxx&app_key=yyy`).

`response` is the answer from the API. To get the data out of it:

```python
data = response.json()
```

`.json()` parses the response body into Python dicts and lists. From here, it's just dictionary navigation — the same skill you've used since Week 2.

---

## API credentials

The TMB API requires two credentials on every request: `app_id` and `app_key`. These prove you're authorized to use the API.

Your teacher will give you these in class. They go in a file called `.env` in your project folder — not in `app.py` itself. The starter code includes a `.env.example` that shows the format:

```
TMB_APP_ID=your_app_id_here
TMB_APP_KEY=your_app_key_here
```

Create a file called `.env` (no extension, just `.env`) and paste in the real values your teacher provides. The app loads them automatically using `python-dotenv`:

```python
from dotenv import load_dotenv
load_dotenv()

TMB_APP_ID = os.getenv("TMB_APP_ID")
TMB_APP_KEY = os.getenv("TMB_APP_KEY")
```

`load_dotenv()` reads the `.env` file. `os.getenv()` pulls individual values by name. The credentials never appear in your Python code — they stay in a separate file.

:::alert{warn}
**The `.env` file is listed in `.gitignore`, so Git won't track it.** This is the correct pattern: credentials live in a file that never gets committed. The `.env.example` file (which has placeholder values, not real keys) *is* committed — it shows other developers what variables they need without exposing the actual secrets.
:::

---

## Navigating the JSON response

The TMB iBus endpoint returns a nested JSON structure. Here's what it looks like for one stop:

```json
{
  "data": {
    "ibus": [
      {"line": "H6", "text-ca": "3 min", "destination": "Feixa Llarga", ...},
      {"line": "75", "text-ca": "8 min", "destination": "Pl. Espanya", ...}
    ]
  }
}
```

To get the raw list of arriving buses:

```python
raw = response.json()["data"]["ibus"]
```

That `["data"]` wrapper is easy to miss. If you write `response.json()["ibus"]`, you'll get a `KeyError`.

The starter code then cleans up each bus entry with a list comprehension — pulling out just the three fields the template needs:

```python
arrivals = [
    {"line": bus["line"], "destination": bus["destination"], "text": bus["text-ca"]}
    for bus in raw
]
```

Note: the API returns the arrival time as `"text-ca"` (Catalan text), not `"text"`. The list comprehension renames it to `"text"` for cleaner template access.

:::alert{info}
The result is a **list of dictionaries** — the same shape you've worked with since Week 2. Each dict has `line`, `text`, and `destination` keys.
:::

---

## Nested loops in the template

Your app queries multiple stops. For each stop, there are multiple arriving buses. That means two levels of data:

```python
stop_boards = [
    {"name": "Zona Universitaria", "arrivals": [{"line": "H6", ...}, {"line": "75", ...}]},
    {"name": "Your Stop",          "arrivals": [{"line": "V3", ...}]},
]
```

The template handles this with a **nested loop** — a `{% for %}` inside another `{% for %}`:

```html
{% for board in stop_boards %}
    <h2>{{ board.name }}</h2>
    <table>
        <tbody>
            {% for bus in board.arrivals %}
            <tr>
                <td>{{ bus.line }}</td>
                <td>{{ bus.destination }}</td>
                <td>{{ bus.text }}</td>
            </tr>
            {% endfor %}
        </tbody>
    </table>
{% endfor %}
```

The outer loop creates a section per stop. The inner loop creates a row per bus. You've done single loops before — this is the same idea, one level deeper.
