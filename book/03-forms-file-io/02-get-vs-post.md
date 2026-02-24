---
name: GET vs POST
index: 2
---

# GET vs POST

When your browser makes a request to a Flask route, it always uses an **HTTP method**. You've been using GET this whole time without thinking about it. POST is new.

## What each one means

**GET** — "Show me something." Used when you type a URL into the address bar, click a link, or load a page. The request is asking for a resource to display.

**POST** — "Here's some data." Used when you submit a form. The request is sending data to be processed — saved, recorded, acted on.

The difference isn't just technical convention. It reflects intent:
- `/display` with GET: "show me the tasks"
- `/input` with POST: "here is a new task to save"

## One route, two behaviors

Your `/input` route handles both. When a user visits `/input` in the browser, Flask runs the GET side and renders the blank form. When the user submits the form, Flask runs the POST side and saves the data.

```python
@app.route("/input", methods=["GET", "POST"])
def input_task():
    if request.method == "POST":
        # User submitted the form — save the data
        ...
        return redirect(url_for("display"))

    # GET: render the blank form
    ...
    return render_template("input.html", projects=projects)
```

Three things to notice:

1. **`methods=["GET", "POST"]`** on the decorator — Flask blocks any method not listed. Omit this and submitting the form gives a `405 Method Not Allowed` error.
2. **`if request.method == "POST"`** — the string is uppercase. This is where you handle the form submission.
3. The GET side is at the bottom — it runs when `request.method` is anything other than `"POST"`.

## What the form's `method` attribute does

When you write `<form method="POST">`, you're telling the browser to send the form data as a POST request. If you omit `method`, the browser defaults to GET — and GET requests don't include form data in the request body, so `request.form` will be empty.

::::tabs{id="method-comparison"}

:::tab{title="With method=\"POST\" (correct)"}
```html
<form action="/input" method="POST">
```
Browser sends the form fields as POST data. Flask's `request.form` contains all the values.
:::

:::tab{title="Without method (broken)"}
```html
<form action="/input">
```
Browser defaults to GET. Form data is sent as URL parameters, not in `request.form`. Flask reads `request.form["title"]` and gets nothing — or a `KeyError`.
:::

::::

:::alert{info}
The `method` attribute on the HTML `<form>` and the `methods=` list on the Flask `@app.route` decorator are two separate things. The form tells the browser how to send the request. The decorator tells Flask which methods to accept. Both must say POST for a form submission to work.
:::
