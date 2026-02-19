---
name: Route
---

A mapping between a URL path and a Python function. When Flask receives a request for `/about`, it looks for a function decorated with `@app.route("/about")` and runs it. The function's return value becomes the HTTP response.
