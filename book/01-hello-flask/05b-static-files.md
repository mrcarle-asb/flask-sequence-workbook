---
name: Static Files
index: 6
---

# Static Files

Your HTML files live in `templates/`. But what about images, CSS, and other files the browser needs to download directly? Those go in a different folder: `static/`.

## The `static/` folder

Flask automatically serves anything inside `static/` at the URL path `/static/`. You don't write a route for it — Flask handles it:

```
your-project/
    app.py
    templates/
        index.html
    static/
        photo.jpg
        style.css
```

If someone visits `/static/photo.jpg`, Flask finds and sends `static/photo.jpg`. That's it.

## Adding an image

The `<img>` tag drops an image onto a page. It's self-closing — no `</img>` needed. `src` tells the browser where to find the file; `alt` is a text description used by screen readers and shown if the image fails to load. In a Flask project, images go in `static/`, and your path starts with `/static/`:

```html
<img src="/static/photo.jpg" alt="a brief description">
```

:::alert{info}
**Broken image icon** (the cracked rectangle): the tag is there and Flask found the URL, but the file isn't at that path. Check the filename and folder.

**Nothing visible at all**: the tag is missing, or there's a typo in the tag itself. Use **CMD-F** to search for `<img` across your files to confirm it exists.
:::

## Adding CSS

A stylesheet works the same way — put it in `static/` and link to it from your HTML `<head>`:

```html
<link rel="stylesheet" href="/static/style.css">
```

## The "proper" way (a preview)

If you search online, you'll often see this pattern instead:

```html
<img src="{{ url_for('static', filename='photo.jpg') }}" alt="...">
```

That `{{ }}` syntax is **Jinja2** — the templating system you'll learn in Week 2. It generates the correct path automatically, which matters when your app is deployed somewhere other than `localhost`. For now, `/static/filename` works fine for local development. You'll switch to `url_for` once Jinja2 makes sense.

:::alert{warn}
If you Google "Flask image" you'll mostly find examples using `url_for`. That's fine code, but it uses Week 2 concepts. Use the `/static/filename` path for now and it will work.
:::
