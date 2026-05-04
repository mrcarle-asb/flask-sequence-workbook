---
name: Going Further
index: 3
---

# Going Further

The assignment uses one small corner of the TMB API. If you finish early or want ideas for a larger project, here's what else is available.

## The four API groups

| API | What it does | Example question it answers |
|-----|-------------|---------------------------|
| **iBus** | Real-time bus arrivals | "When is the next H6 at my stop?" |
| **Transit** | Network topology — all lines, stops, stations | "What stops are on bus line 22?" |
| **Planner** | Journey routing between two locations | "How do I get from Gràcia to the Fòrum by transit?" |
| **GTFS** | Full timetable download (ZIP of CSVs) | "Which stops have no service after 10 PM on Sundays?" |

You used iBus this week. The other three are available at the same base URL (`api.tmb.cat/v1/`) with the same credentials.

Your project resources folder has a full student guide covering all four groups with endpoint details, response formats, and example use cases.

---

## Quick extensions for this assignment

**Handle empty arrivals.** Some stops have no buses right now (late at night, or a stop temporarily out of service). Add a conditional in the template:

```html
{% if board.arrivals %}
    <table>...</table>
{% else %}
    <p>No buses scheduled right now.</p>
{% endif %}
```

**Auto-refresh.** Bus arrivals go stale quickly. Add this line inside `<head>` in `base.html` to reload the page every 30 seconds:

```html
<meta http-equiv="refresh" content="30">
```

**Notice the slowness.** With 3 stops, the page takes a moment to load. Why? Your app calls the TMB API three times *in sequence* — it waits for each response before starting the next. With more stops, it gets worse. Solving this requires async programming, which is beyond this assignment's scope, but it's worth noticing the problem.

---

## Bigger project ideas

These are starting points, not assignments. Any of them could become a more substantial project:

- **Line explorer** — enter a bus line number, see all its stops on a map with live arrivals (Transit + iBus APIs)
- **Commute planner** — enter start and end addresses, show the best transit route (Planner API)
- **Schedule analyser** — download the GTFS data, load it into SQLite, and query it (GTFS API + SQL skills from Week 4)
