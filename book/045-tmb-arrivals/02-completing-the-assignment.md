---
name: Completing the Assignment
index: 2
---

# Completing the Assignment

The app is already fully functional with one stop. Your job is to understand the data flow, then add your own stops.

## Step 0: Read the code

Open `app.py`. It's short. Trace the flow:

1. `load_dotenv()` reads credentials from the `.env` file
2. `STOPS` is a list of dicts, each with an `id` and a `name`
3. The `index()` route loops over `STOPS`
4. For each stop, it calls the TMB API with `requests.get()`
5. It pulls the raw arrivals from `response.json()["data"]["ibus"]`, then cleans them with a list comprehension
6. It builds `stop_boards` — a list of dicts, each with a `name` and an `arrivals` list
7. It passes `stop_boards` to the template

Then open `templates/index.html`. The outer loop creates a section per stop; the inner loop creates a row per bus. Nothing here needs to change — it already handles any number of stops.

---

## Step 1: Set up your credentials

The starter code includes a `.env.example` file. Create a new file called `.env` (just `.env`, no other extension) in the same folder, and paste in the real credentials your teacher provided:

```
TMB_APP_ID=the_actual_id
TMB_APP_KEY=the_actual_key
```

No quotes, no spaces around the `=`. The app reads this file automatically via `load_dotenv()`.

---

## Step 2: Test with the default stop

Run `flask run --debug` and open the app. You should see a "Zona Universitaria" section with a table of live bus arrivals — line numbers, destinations, and countdown times.

If you see an error instead, check:
- Does your `.env` file exist in the project folder (not inside `templates/` or anywhere else)?
- Did you paste both values (app_id and app_key)?
- Is your Codespace connected to the internet?

---

## Step 3: Find your stops

You need two bus stops from your own life — near your home, a friend's place, a café, wherever. You need the **stop ID**, which is a 3–5 digit number.

Two ways to find it:

**On the street:** Every TMB bus stop has a sign with its code printed on it (usually a small number near the stop name).

**Online:** Go to [tmb.cat](https://www.tmb.cat), search for a stop or line, and look at the URL. The stop code appears in the address bar.

---

## Step 4: Add your stops

In `app.py`, uncomment and fill in the TODO lines in the `STOPS` list:

```python
STOPS = [
    {"id": 556, "name": "Zona Universitaria"},
    {"id": 1265, "name": "Pg. de Gràcia - Aragó"},
    {"id": 3024, "name": "Pl. Espanya"},
]
```

Use your own stop IDs and names. Save the file and the page should update automatically (debug mode).

---

## Common mistakes

### Wrong JSON path

```python
# WRONG — KeyError
arrivals = response.json()["ibus"]

# CORRECT — need the "data" wrapper
arrivals = response.json()["data"]["ibus"]
```

The TMB response has a `"data"` layer around the `"ibus"` array. If you modify the API call code and get a `KeyError`, this is probably why.

### Missing `"name"` key in STOPS entry

```python
# WRONG — missing "name"
{"id": 1265}

# CORRECT
{"id": 1265, "name": "Pg. de Gràcia - Aragó"}
```

The template uses `{{ board.name }}` for the section heading. If the dict doesn't have a `"name"` key, you'll get a Jinja2 error.

### Stop not in the TMB bus network

Some stops only serve metro or tram — the iBus API only tracks buses. If a stop returns no arrivals, it might not be a bus stop. Try a different one.

### `.env` file in the wrong place

The `.env` file must be in the project root — the same folder as `app.py`. If you put it inside `templates/` or somewhere else, `load_dotenv()` won't find it and `os.getenv()` will return `None`, causing authentication failures.

### Committing the `.env` file

The `.gitignore` already excludes `.env`, so this shouldn't happen. But if you renamed the file or created it differently, double-check that `git status` does NOT show `.env` as a tracked file. The `.env.example` (with placeholder values) *is* committed — that's intentional.

---

## Checklist

- [ ] `.env` file exists in the project root with real credentials
- [ ] `.env` is NOT showing in `git status` (gitignored correctly)
- [ ] App runs and shows live arrivals for Zona Universitaria
- [ ] Two personal stops added to `STOPS` with correct IDs and names
- [ ] All three stops display arrival data on the page
