---
name: "Week 4.5: TMB Bus Arrivals"
index: 5
---

# Week 4.5: TMB Bus Arrivals

Another side quest. This time your Flask app talks to the internet.

Until now, every piece of data in your app came from somewhere local — a Python list, a CSV file, a SQLite database. This week, your app calls an **API** (a URL that returns data instead of a web page) and displays live bus arrival times from Barcelona's TMB transit network.

:::alert{warn}
Same rule as always: **run the starter code first.** The app already works with one stop. Your job is to understand what it does, then add your own stops.
:::

## What you already know

- Flask routes, `render_template()`, template inheritance
- Jinja2 loops (`{% for ... %}`)
- Passing lists of dictionaries to templates

## What's new this week

- **`requests` library** — making HTTP calls from Python
- **API credentials** — authenticating with a third-party service
- **JSON responses** — parsing structured data from an API
- **Nested loops** — a loop inside a loop in your template

## Why it matters

Most real web apps don't generate all their own data. They pull from APIs — weather, maps, payments, social media, transit. After this week, you know the basic pattern: call a URL, get JSON back, render it in a template. That pattern scales to any API.
