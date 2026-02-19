---
name: Running Your App
index: 3
---

# Running Your App

You've written the code. Now you need to actually start the server.

## In Codespaces (the easy way)

Open the terminal in VS Code and run:

```
flask run --debug
```

Codespaces will detect the running server and offer to forward the port. Click **Open in Browser**.

## What those words mean

- **`flask`** — the command-line tool that comes with the Flask library
- **`run`** — start the development server
- **`--debug`** — enable auto-reload. When you save a file, the server restarts automatically. Always use this during development.

The terminal will show:

```
 * Running on http://127.0.0.1:5000
```

:::alert{info}
`127.0.0.1` is :t[localhost] — it means "this computer." Your Flask server is running locally. In Codespaces, port forwarding handles this transparently.
:::

## The server keeps running

This is different from a normal Python script. A script runs and finishes. A Flask server runs and **waits** — it's listening for requests. You'll see the terminal "hang" with no new prompt. That's normal. It's working.

To stop the server: `Ctrl+C` in the terminal.

## The alternative: `app.run()`

You might also see this pattern at the bottom of `app.py`:

```python
if __name__ == "__main__":
    app.run(debug=True)
```

This lets you run the app with `python app.py` instead of `flask run --debug`. Same result, different command. The starter code includes this so both approaches work.

## When things go wrong

**`flask: command not found`** — Flask isn't installed. In Codespaces this shouldn't happen (it's pre-installed). Locally, you'd need `pip install flask`.

**The page shows an error** — Read the terminal output. Flask error messages are verbose but helpful. The trick: your mistake might be on line 12, but the crash might show up at line 346 inside some Flask internal file. Look for *your* filename in the traceback.

:::alert{warn}
Your error messages will sometimes point deep into Flask's own code. Don't panic. Scroll through the traceback and find the line from **your** file — that's where the actual problem is.
:::
