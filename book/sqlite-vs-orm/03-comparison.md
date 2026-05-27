---
name: "Comparison"
index: 3
---

# Side by Side

This page puts the two approaches next to each other. Same app, same data, same templates — different code in the middle. Read through each pair and notice what changes and what doesn't.

---

## Defining the schema

### sqlite3

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

A separate `.sql` file. You read it. You run it. You maintain it alongside your Python code.

### Flask-SQLAlchemy

```python
# models.py

class Author(db.Model):
    __tablename__ = "authors"
    author_id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String, nullable=False)
    birth = db.Column(db.String)
    death = db.Column(db.String)
    books = db.relationship("Book", back_populates="author")

class Book(db.Model):
    __tablename__ = "books"
    book_id = db.Column(db.Integer, primary_key=True)
    author_id = db.Column(db.Integer, db.ForeignKey("authors.author_id"), nullable=False)
    title = db.Column(db.String, nullable=False)
    publication_year = db.Column(db.Integer)
    author = db.relationship("Author", back_populates="books")
```

The schema is Python code. One file defines both the structure and the relationships. No `.sql` file to keep in sync.

**The trade-off:** sqlite3's schema is shorter and you can read it as plain SQL. The ORM schema is more verbose but includes relationship declarations that pay off later.

---

## Creating the database

### sqlite3

```python
# init_db.py
import sqlite3
connection = sqlite3.connect("books.db")
with open("schema.sql") as f:
    connection.executescript(f.read())
connection.commit()
connection.close()
```

### Flask-SQLAlchemy

```python
# init_db.py
from app import app
from models import db
with app.app_context():
    db.create_all()
```

The ORM reads the model classes and builds the tables. With sqlite3, you read and run a SQL script yourself.

---

## Reading data (SELECT / query)

### sqlite3

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

You write the JOIN yourself. You alias `authors.name AS author_name` so the template has a clean field name. You manage the connection.

### Flask-SQLAlchemy

```python
@app.route("/")
def list_books():
    books = Book.query.order_by(Book.title).all()
    return render_template("index.html", books=books)
```

No JOIN, no SQL string, no connection management. The template accesses `book.author.name` through the relationship.

**The trade-off:** The sqlite3 version makes you think about what data you're actually pulling from the database. The ORM version hides that — convenient, but you lose visibility into exactly which queries run.

---

## Creating a record (INSERT / add)

### sqlite3

```python
if request.method == "POST":
    with get_db() as conn:
        conn.execute(
            "INSERT INTO books (author_id, title, publication_year) VALUES (?, ?, ?)",
            (request.form["author_id"], request.form["title"], request.form["year"])
        )
        conn.commit()
    return redirect(url_for("list_books"))
```

### Flask-SQLAlchemy

```python
if request.method == "POST":
    new_book = Book(
        author_id=request.form["author_id"],
        title=request.form["title"],
        publication_year=request.form["year"]
    )
    db.session.add(new_book)
    db.session.commit()
    return redirect(url_for("list_books"))
```

Similar length. The ORM version reads more like "create a book and save it." The sqlite3 version reads more like "send this INSERT command to the database."

---

## Updating a record

### sqlite3

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

    with get_db() as conn:
        book = conn.execute("SELECT * FROM books WHERE book_id = ?", (book_id,)).fetchone()
        authors = conn.execute("SELECT * FROM authors ORDER BY name").fetchall()
    return render_template("edit.html", book=book, authors=authors)
```

Two separate operations: SELECT to load the form, UPDATE to save changes. Two separate connections.

### Flask-SQLAlchemy

```python
@app.route("/edit/<int:book_id>", methods=["GET", "POST"])
def edit_book(book_id):
    book = db.session.get(Book, book_id)

    if request.method == "POST":
        book.title = request.form["title"]
        book.publication_year = request.form["year"]
        book.author_id = request.form["author_id"]
        db.session.commit()
        return redirect(url_for("list_books"))

    authors = Author.query.order_by(Author.name).all()
    return render_template("edit.html", book=book, authors=authors)
```

Load the object once. Change its attributes. Commit. The ORM tracks what changed — you don't write an UPDATE statement.

**This is where the approaches start to feel genuinely different.** The sqlite3 version has you writing two distinct SQL operations. The ORM version has you modifying a Python object and trusting the library to write the SQL.

---

## Deleting a record

### sqlite3

```python
@app.route("/delete/<int:book_id>", methods=["POST"])
def delete_book(book_id):
    with get_db() as conn:
        conn.execute("DELETE FROM books WHERE book_id = ?", (book_id,))
        conn.commit()
    return redirect(url_for("list_books"))
```

### Flask-SQLAlchemy

```python
@app.route("/delete/<int:book_id>", methods=["POST"])
def delete_book(book_id):
    book = db.session.get(Book, book_id)
    db.session.delete(book)
    db.session.commit()
    return redirect(url_for("list_books"))
```

The ORM version loads the object first, then deletes it. The sqlite3 version sends one command. For a simple delete, the raw SQL is actually shorter.

---

## The templates

### sqlite3 version

```html
<td>{{ book.title }}</td>
<td>{{ book.author_name }}</td>
<td>{{ book.publication_year }}</td>
```

### Flask-SQLAlchemy version

```html
<td>{{ book.title }}</td>
<td>{{ book.author.name }}</td>
<td>{{ book.publication_year }}</td>
```

One line is different: `book.author_name` (a SQL alias) vs `book.author.name` (a relationship traversal). Everything else is identical.

The template doesn't care how the data got there. It cares about the names of the variables it receives. If you keep those consistent, you can swap the entire database layer without touching a single template.

There's a CS concept floating around here. Something about abstraction, decomposition, and the value of clean interfaces between layers of a system.

---

## When to use which

There's no universal answer. But here's a framework:

### sqlite3 is a good fit when:

- Your schema is flat — one or two tables, no foreign keys
- You want to learn (or teach) SQL itself, not hide it
- You want minimal dependencies — `sqlite3` ships with Python, nothing to install
- The project is small and you want to see exactly what's happening
- You're comfortable writing and debugging SQL strings

### Flask-SQLAlchemy is a good fit when:

- Your tables have relationships — books belong to authors, tasks belong to projects, students take courses
- You want `book.author.name` instead of writing JOIN queries
- You want derived values and methods on your data (`author.book_count`, `task.is_overdue`)
- The project is large enough that a `models.py` file is worth maintaining
- You're working on a team and "schema as code" is clearer than a separate `.sql` file

### The honest version:

For a project with one table and no relationships, Flask-SQLAlchemy feels like overhead — you're writing a class and configuring a library to avoid writing a few SQL strings. The ceremony isn't worth it.

For a project with three or more tables that reference each other, raw `sqlite3` gets painful fast. Every read operation needs a JOIN. Every form needs manual connection management. The ORM's upfront cost starts paying for itself.

The crossover point is usually around the second table with a foreign key. When you find yourself writing the same JOIN in four different routes, the relationship syntax starts looking less like overhead and more like the point.
