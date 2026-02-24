---
name: Check Yourself
index: 8
---

# Check Yourself

## Conceptual

:::collapsible{title="What's the difference between GET and POST?"}
**GET** asks the server to show something — used when you visit a URL or click a link. **POST** sends data to the server — used when you submit a form. GET requests don't carry form data in the request body. POST does.
:::

:::collapsible{title="What does the name= attribute on a form input do?"}
It becomes the key in `request.form`. When the form is submitted, the browser labels each field's value with its `name`. Flask reads those labels. `name="title"` → `request.form["title"]`. Without `name`, Flask can't find that field's value at all.
:::

:::collapsible{title="Why redirect after POST instead of using render_template?"}
If you render a page after a POST, the browser's last request was a POST. When the user refreshes, the browser resends the POST — submitting the form again and creating a duplicate row. Redirecting after POST sends the browser to a GET request. Refreshing that just re-runs the GET, not the POST.
:::

:::collapsible{title="Why use \"a\" mode and not \"w\" when writing the CSV?"}
`"w"` (write) truncates the file to zero before writing anything. Every form submission deletes all previous rows and replaces them with just the new one. `"a"` (append) positions the write cursor at the end of the file, leaving existing rows untouched.
:::

:::collapsible{title="What does url_for(\"display\") do that redirect(\"/display\") doesn't?"}
`url_for` generates the URL from the **function name**, not the path. If you ever change the route from `/display` to something else, `url_for("display")` still works — it finds the function and generates the new path. A hardcoded `"/display"` would silently break.
:::

:::collapsible{title="What does csv.DictReader give you, and how does it know the column names?"}
`DictReader` reads each row of the CSV as a dictionary. It uses the **first row of the file** (the header row) as the dictionary keys. If your CSV has `datetime,project_title,title,...` as its first line, each row becomes `{"datetime": "...", "project_title": "...", "title": "...", ...}` — exactly the shape Jinja expects for `{{ task.title }}`.
:::

## Practical

::::tabs

:::tab{title="Can you..."}
- [ ] Build an HTML form with the correct `action`, `method`, and `name` attributes?
- [ ] Read a submitted field with `request.form["field_name"]`?
- [ ] Handle GET and POST in the same route using `request.method`?
- [ ] Append a new row to a CSV with `csv.DictWriter` in `"a"` mode?
- [ ] Read all rows from a CSV with `csv.DictReader`?
- [ ] Redirect to a different route after a successful POST?
- [ ] Use `url_for("function_name")` to generate a route URL?
- [ ] Submit a task and confirm it persists after a server restart?
:::

:::tab{title="Read this code"}
```python
@app.route("/input", methods=["GET", "POST"])
def input_task():
    if request.method == "POST":
        title = request.form["title"]
        with open("tasks.csv", "a", newline="") as f:
            writer = csv.DictWriter(f, fieldnames=["title"])
            writer.writerow({"title": title})
        return redirect(url_for("display"))
    return render_template("input.html")
```

What happens if you submit this form and then immediately press refresh in the browser?

:::collapsible{title="Answer"}
The page refreshes by re-running a GET request to whatever URL you were redirected to — in this case `/display`. It does **not** re-submit the form. The POST-redirect-GET pattern breaks the refresh cycle: the last request the browser made was a GET (the redirect), so refreshing repeats the GET, not the POST.

If the code had used `render_template` instead of `redirect`, refreshing would have re-submitted the POST and added a duplicate row.
:::
:::

::::

## What's coming next

Your task tracker records entries, but it can't edit or delete them. That changes in Week 5. First, Week 4 takes a detour: a Birthdays app that reinforces everything you know so far with a different dataset and slightly more complexity. Same patterns, different context — exactly the kind of repetition that makes the skills stick.
