---
name: Redirect and url_for
index: 5
---

# Redirect and url_for

After saving a task, you need to send the user somewhere. The obvious move is `render_template("display.html", ...)`. Don't do that.

## The refresh problem

If you `render_template` after a POST:

1. User submits form → POST → data saved → page renders
2. User presses browser refresh
3. Browser says "resend the POST request?"
4. User clicks OK → data saved **again** — a duplicate row in your CSV

This keeps happening every time they refresh. In a task tracker, that's annoying. In an e-commerce checkout, it's a billing disaster.

## POST-redirect-GET

The fix is to redirect after every successful POST:

```python
@app.route("/input", methods=["GET", "POST"])
def input_task():
    if request.method == "POST":
        # ... read form, write to CSV ...
        return redirect(url_for("display"))  # <-- redirect, not render_template

    return render_template("input.html", projects=projects)
```

Now the sequence is:

1. User submits form → POST → data saved → **redirect to `/display`**
2. Browser follows the redirect → GET request to `/display` → page renders
3. User presses refresh → re-runs the GET, not the POST → no duplicate

This pattern is called **POST-redirect-GET** and it's standard practice for any form submission that modifies data.

## `url_for()` vs a hardcoded string

You could write:

```python
return redirect("/display")
```

That works. But `url_for("display")` is better:

```python
return redirect(url_for("display"))
```

`url_for` takes a **function name** (the Python function for the route, as a string) and generates the correct URL. If you ever rename the route path from `/display` to `/tasks/all`, the hardcoded string breaks silently. `url_for("display")` still works — it looks up the function, not the path.

:::alert{info}
`url_for` requires the **function name**, not the route path. The function for `/display` is named `display`, so you write `url_for("display")`. The function for `/input` is named `input_task`, so `url_for("input_task")`.
:::

## The datetime stamp

One last piece before you write the POST handler. Every row needs a timestamp:

```python
from datetime import datetime

datetime.now().strftime("%Y-%m-%d %H:%M:%S")
# → "2025-10-07 09:15:00"
```

Call this inside the POST block, not at module level — you want the time of submission, not the time the server started.

The import line already includes `datetime` in your starter code. You just need to call it when building the row dict.
