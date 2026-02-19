---
name: What is Flask?
index: 1
---

# What is Flask?

Flask is a :t[web framework] written in Python. A web framework is a program that listens for :t[HTTP] requests and runs Python functions to generate responses.

## Static vs. Dynamic

**Without Flask** — the old, static web. Every page is a complete `.html` file stored on a server. Ask for a page, get a file. Ask 1000 times, get the same file 1000 times.

**With Flask** — the dynamic web. Every page is *generated* by a Python function, in response to each request. The page can be different every time.

Flask is a tool designed to make this process "easy."

:::alert{warn}
It will not seem easy. Flask is complex. But learning how to use this complex tool is much easier than doing all of the Flask functions by hand — which is what you were doing last week when you generated HTML from a Python script.
:::

## The five-line Hello World

Here is the smallest possible Flask application:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def index():
    return "<h1>Hello, World!</h1>"
```

That's it. Five lines, and you have a web server. The rest of this section breaks down each line.
