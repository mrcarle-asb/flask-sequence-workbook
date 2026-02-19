---
name: The Hello World, Line by Line
index: 2
---

# The Hello World, Line by Line

Let's read the minimal Flask program the way you'd read any unfamiliar code — one line at a time, asking "what does this do and why?"

## The import

```python
from flask import Flask
```

`flask` (lowercase) is the :t[library]. `Flask` (uppercase) is the :t[class].

"But those are the same word!" — almost. Python is case sensitive. There's also a :t[convention] at work: libraries are lowercase, classes are capitalized. This isn't a language rule, just a practice that Python programmers follow.

Think of lowercase `flask` as a huge collection of LEGO. Capital `Flask` is a specific box from that collection — the one that builds the main robot. All the other pieces in the `flask` library extend and build on that robot.

This line opens the collection and pulls out the main box.

## Building the app

```python
app = Flask(__name__)
```

This line builds the LEGO robot from the `Flask` kit. In OOP terms, it calls the :t[constructor] — the function that creates a new Flask object.

Now we have a Flask robot named `app`. Every time you see `app.something` in the code, we're talking to this particular robot — calling one of its methods or reading one of its properties.

**What's `__name__`?** Python has internal variables identified by double underscores. `__name__` is a flag that identifies how this Python file is being run. For now, think of it as telling Flask "the name of this program." The details don't matter yet.

**`app` is just a variable name.** It's convention, not magic. But every Flask tutorial uses it, so we follow convention.

## The route decorator

```python
@app.route("/")
def index():
    return "<h1>Hello, World!</h1>"
```

This is three things at once. Let's unpack from the inside out.

### The path: `"/"`

`/` is the root path. Remember, :t[URL]s have the structure:

```
computer:port/resource
```

So `localhost:5000/` means "the root resource." Instead of requesting a specific file, requesting `/` is like knocking on someone's door and saying "Hey, whaddaya got?"

Even if you have nothing else to share, you have something at `/`.

### The method: `.route("/")`

`.route()` is a method that belongs to our `app` object. It's the same dot-method pattern you've used before — like `my_list.sort()` or `random.randint()`.

Somewhere inside the Flask library, there's code that defines how `.route()` talks to the HTTP server. We don't need to read that code. We just need to know that our robot has a `.route()` superpower that connects it to the HTTP world.

:::alert{info}
This is what libraries do. They provide tools to do complex tasks in a less complex fashion. They hide complexity. They are :t[abstraction]s.
:::

### The decorator: `@`

The `@` symbol is a Python :t[decorator]. Decorators mark a chunk of code that should run at a *particular moment*, rather than top-to-bottom as Python reads the file.

`@app.route("/")` is a trigger — like event handlers in CMU Graphics. It says:

> "When the Flask app receives an HTTP request matching `/`, run the function directly below this line."

The function doesn't run when Python reads it. It runs when a browser asks for that URL.

## The function

```python
def index():
    return "<h1>Hello, World!</h1>"
```

A plain Python function. It **returns** a string. That string goes back through Flask, back to the HTTP server, back through the network, and arrives at the browser that made the request.

:::alert{error}
**Return, not print.** This is the #1 stumbling point. You are used to `print()` being how you show output. In Flask, `return` sends data to the browser. `print()` only shows in your terminal — the user never sees it.
:::

The function name `index` is convention — it matches the `/` route, like `index.html` is the default page on a web server. But the name could be anything. Flask cares about the decorator, not the function name.
