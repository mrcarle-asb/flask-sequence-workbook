---
name: Decorator
---

A Python feature using the `@` symbol. A decorator modifies the function defined directly below it. In Flask, `@app.route("/path")` is a decorator that registers the function as a handler for that URL path. The function runs when a matching request arrives, not when Python reads the file top-to-bottom. Think of it as an event trigger.
