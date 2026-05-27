---
name: "sqlite3 Basics"
index: 1
---

# sqlite3 Basics

Python ships with a built-in module called `sqlite3` that talks directly to SQLite databases. No extra packages to install. You write SQL as strings, send them to the database, and get rows back.

This page walks through a complete Flask app — a book catalogue with authors — using only `sqlite3`. Full CRUD: create, read, update, delete.

---

## The schema

You define your tables in a `.sql` file. This is plain SQL — the same syntax you'd use in any SQLite tool.

```sql
-- schema.sql

CREATE TABLE IF NOT EXISTS authors (
    author_id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    birth TEXT,
    death TEXT
);

CREATE TABLE IF NOT EXISTS books (
    book_id INTEGER PRIMARY KEY AUTOINCREMENT,
    author_id INTEGER NOT NULL,
    title TEXT NOT NULL,
    publication_year INTEGER,
    FOREIGN KEY (author_id) REFERENCES authors (author_id)
);
```

Two things to notice:

- `AUTOINCREMENT` means you never supply the ID yourself — the database picks the next number.
- `FOREIGN KEY (author_id) REFERENCES authors (author_id)` is the relationship. Every book points to exactly one author. The database will reject an `author_id` that doesn't exist in the `authors` table (if you enable foreign key enforcement — more on that below).

---

## Database setup

You run the schema once to create the `.db` file. A small script does this:

```python
# init_db.py

import sqlite3

connection = sqlite3.connect("books.db")
with open("schema.sql") as f:
    connection.executescript(f.read())

connection.execute("INSERT INTO authors (name, birth, death) VALUES (?, ?, ?)",
                   ("Ursula K. Le Guin", "1929-10-21", "2018-01-22"))
connection.execute("INSERT INTO authors (name, birth) VALUES (?, ?)",
                   ("J.R.R. Tolkien", "1892-01-03"))
connection.execute("INSERT INTO authors (name, birth) VALUES (?, ?)",
                   ("Chimamanda Ngozi Adichie", "1977-09-15"))

connection.execute("INSERT INTO books (author_id, title, publication_year) VALUES (?, ?, ?)",
                   (1, "The Left Hand of Darkness", 1969))
connection.execute("INSERT INTO books (author_id, title, publication_year) VALUES (?, ?, ?)",
                   (1, "A Wizard of Earthsea", 1968))
connection.execute("INSERT INTO books (author_id, title, publication_year) VALUES (?, ?, ?)",
                   (2, "The Hobbit", 1937))
connection.execute("INSERT INTO books (author_id, title, publication_year) VALUES (?, ?, ?)",
                   (3, "Americanah", 2013))

connection.commit()
connection.close()
print("Database created.")
```

Run it once: `python init_db.py`. Now you have a `books.db` file. You don't run this again unless you want to start over.

---

## Connecting in Flask

Every route that touches the database needs a connection. The `sqlite3` context manager handles cleanup:

```python
# app.py

from flask import Flask, render_template, request, redirect, url_for
import sqlite3

app = Flask(__name__)
DATABASE = "books.db"


def get_db():
    conn = sqlite3.connect(DATABASE)
    conn.row_factory = sqlite3.Row
    conn.execute("PRAGMA foreign_keys = ON")
    return conn
```

Three things happen in `get_db()`:

1. **`sqlite3.connect(DATABASE)`** opens the file (or creates it if missing).
2. **`conn.row_factory = sqlite3.Row`** makes query results behave like dictionaries. Without this, you get plain tuples and your templates can't use `{{ book.title }}`.
3. **`PRAGMA foreign_keys = ON`** tells SQLite to actually enforce foreign key constraints. SQLite has them off by default — a common surprise.

---

## Read (SELECT)

The index page lists all books with their author names. This requires a JOIN — the book table has `author_id`, not the author's name.

```python
@app.route("/")
def list_books():
    with get_db() as conn:
        books = conn.execute("""
            SELECT books.book_id, books.title, books.publication_year,
                   authors.name AS author_name
            FROM books
            JOIN authors ON books.author_id = authors.author_id
            ORDER BY books.title
        """).fetchall()
    return render_template("index.html", books=books)
```

`fetchall()` returns a list of `sqlite3.Row` objects. Each one is dict-like — `book["title"]` and `book.title` both work — so the template can use `{{ book.title }}` directly.

:::collapsible{title="Why the JOIN?"}
The `books` table stores `author_id`, not the author's name. If you just `SELECT * FROM books`, each row has a number like `1` or `2` where you want "Ursula K. Le Guin." The JOIN pulls the name from the `authors` table and aliases it as `author_name` so the template has a clean field to use.
:::

---

## Create (INSERT)

A form submits a new book. The POST route inserts it:

```python
@app.route("/add", methods=["GET", "POST"])
def add_book():
    if request.method == "POST":
        with get_db() as conn:
            conn.execute(
                "INSERT INTO books (author_id, title, publication_year) VALUES (?, ?, ?)",
                (request.form["author_id"], request.form["title"], request.form["year"])
            )
            conn.commit()
        return redirect(url_for("list_books"))

    # GET — show the form, with authors for the dropdown
    with get_db() as conn:
        authors = conn.execute("SELECT * FROM authors ORDER BY name").fetchall()
    return render_template("add.html", authors=authors)
```

The `?` placeholders are critical. Never use f-strings or `.format()` to build SQL — that's how SQL injection happens. The `?` lets `sqlite3` handle escaping.

The GET branch queries all authors so the template can build a `<select>` dropdown.

---

## Update

Updating requires two steps: load the current values (GET), then save the changes (POST).

```python
@app.route("/edit/<int:book_id>", methods=["GET", "POST"])
def edit_book(book_id):
    if request.method == "POST":
        with get_db() as conn:
            conn.execute(
                "UPDATE books SET title = ?, publication_year = ?, author_id = ? WHERE book_id = ?",
                (request.form["title"], request.form["year"], request.form["author_id"], book_id)
            )
            conn.commit()
        return redirect(url_for("list_books"))

    # GET — load current values for the form
    with get_db() as conn:
        book = conn.execute("SELECT * FROM books WHERE book_id = ?", (book_id,)).fetchone()
        authors = conn.execute("SELECT * FROM authors ORDER BY name").fetchall()
    return render_template("edit.html", book=book, authors=authors)
```

Notice the GET branch runs two queries — one for the book, one for the author dropdown. The template needs both.

---

## Delete

Delete is a single POST. The book ID comes from a hidden form field in the template.

```python
@app.route("/delete/<int:book_id>", methods=["POST"])
def delete_book(book_id):
    with get_db() as conn:
        conn.execute("DELETE FROM books WHERE book_id = ?", (book_id,))
        conn.commit()
    return redirect(url_for("list_books"))
```

---

## The template

Here's the key part of `index.html`. This template doesn't know it's talking to `sqlite3` — it just receives a list of dict-like objects and loops through them:

```html
<h1>Book Catalogue</h1>

<table>
  <thead>
    <tr>
      <th>Title</th>
      <th>Author</th>
      <th>Year</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    {% for book in books %}
    <tr>
      <td>{{ book.title }}</td>
      <td>{{ book.author_name }}</td>
      <td>{{ book.publication_year }}</td>
      <td>
        <a href="{{ url_for('edit_book', book_id=book.book_id) }}">Edit</a>
        <form method="post" action="{{ url_for('delete_book', book_id=book.book_id) }}" style="display:inline">
          <button type="submit">Delete</button>
        </form>
      </td>
    </tr>
    {% endfor %}
  </tbody>
</table>

<a href="{{ url_for('add_book') }}">Add a book</a>
```

This works because `row_factory = sqlite3.Row` makes each row accessible by column name. The template uses `book.title`, `book.author_name`, `book.publication_year` — all names that came from the SQL query.

:::alert{info}
Remember this template. You'll see a nearly identical one on the Flask-SQLAlchemy page. The field names are the same; only the code that produces them changes.
:::
