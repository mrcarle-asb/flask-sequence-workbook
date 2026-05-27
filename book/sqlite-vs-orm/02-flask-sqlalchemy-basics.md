---
name: "Flask-SQLAlchemy Basics"
index: 2
---

# Flask-SQLAlchemy Basics

Flask-SQLAlchemy is an ORM — an Object-Relational Mapper. Instead of writing SQL strings, you define your tables as Python classes and interact with the database through objects and methods.

This page walks through the same book catalogue app from the sqlite3 page, rebuilt with Flask-SQLAlchemy. Same data, same templates, different interface.

---

## The schema — as Python classes

With `sqlite3`, the schema lives in a `.sql` file. With Flask-SQLAlchemy, the schema **is** Python code:

```python
# models.py

from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()


class Author(db.Model):
    __tablename__ = "authors"

    author_id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String, nullable=False)
    birth = db.Column(db.String)
    death = db.Column(db.String)

    books = db.relationship("Book", back_populates="author")

    def __repr__(self):
        return f"<Author {self.name}>"


class Book(db.Model):
    __tablename__ = "books"

    book_id = db.Column(db.Integer, primary_key=True)
    author_id = db.Column(db.Integer, db.ForeignKey("authors.author_id"), nullable=False)
    title = db.Column(db.String, nullable=False)
    publication_year = db.Column(db.Integer)

    author = db.relationship("Author", back_populates="books")

    def __repr__(self):
        return f"<Book {self.title}>"
```

This is more code than a `.sql` file for the same two tables. That's the upfront cost. Here's what you get for it:

- **`db.relationship("Book", back_populates="author")`** — this is the big one. It means once you have an author object, `author.books` gives you all their books. And given a book, `book.author.name` gives you the author's name. No JOIN query needed.
- **`nullable=False`** is the equivalent of `NOT NULL` in SQL.
- **`db.ForeignKey("authors.author_id")`** declares the relationship at the database level — same as the `FOREIGN KEY` clause in raw SQL.
- **`__repr__`** is optional but useful for debugging. When you `print(book)`, you see `<Book The Hobbit>` instead of `<Book object at 0x...>`.

:::collapsible{title="Why a separate models.py?"}
You *can* put the model classes in `app.py`. But as your app grows, separating models keeps the file manageable. The pattern is: `models.py` defines the classes, `app.py` imports them. This is convention, not a requirement.
:::

---

## Configuration and setup

```python
# app.py

from flask import Flask, render_template, request, redirect, url_for
from models import db, Author, Book

app = Flask(__name__)
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///books.db"
db.init_app(app)
```

The URI `"sqlite:///books.db"` means "use a SQLite file called `books.db` in the project directory." Three slashes — two for the protocol, one for a relative path.

To create the tables and seed some data:

```python
# init_db.py

from app import app
from models import db, Author, Book

with app.app_context():
    db.create_all()

    a1 = Author(name="Ursula K. Le Guin", birth="1929-10-21", death="2018-01-22")
    a2 = Author(name="J.R.R. Tolkien", birth="1892-01-03")
    a3 = Author(name="Chimamanda Ngozi Adichie", birth="1977-09-15")
    db.session.add_all([a1, a2, a3])
    db.session.commit()

    db.session.add_all([
        Book(author_id=a1.author_id, title="The Left Hand of Darkness", publication_year=1969),
        Book(author_id=a1.author_id, title="A Wizard of Earthsea", publication_year=1968),
        Book(author_id=a2.author_id, title="The Hobbit", publication_year=1937),
        Book(author_id=a3.author_id, title="Americanah", publication_year=2013),
    ])
    db.session.commit()
    print("Database created.")
```

`db.create_all()` reads your model classes and creates the tables. No `.sql` file to maintain separately — the schema is already defined in `models.py`.

---

## Read (query)

```python
@app.route("/")
def list_books():
    books = Book.query.order_by(Book.title).all()
    return render_template("index.html", books=books)
```

That's it. No connection to open. No cursor. No SQL string. No JOIN.

Wait — where does the author's name come from? The template uses `book.author.name`. Because of the `db.relationship()` you defined in the model, SQLAlchemy loads the related author automatically. The JOIN happens behind the scenes.

```python
# If you need to filter:
tolkien_books = Book.query.filter_by(author_id=2).all()

# If you need one book by ID:
book = db.session.get(Book, 3)
```

---

## Create

```python
@app.route("/add", methods=["GET", "POST"])
def add_book():
    if request.method == "POST":
        new_book = Book(
            author_id=request.form["author_id"],
            title=request.form["title"],
            publication_year=request.form["year"]
        )
        db.session.add(new_book)
        db.session.commit()
        return redirect(url_for("list_books"))

    authors = Author.query.order_by(Author.name).all()
    return render_template("add.html", authors=authors)
```

Instead of writing an INSERT statement, you create a Python object and add it to the session. `db.session.commit()` writes it to the database.

---

## Update

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

No UPDATE statement. You modify the object's attributes — `book.title = ...` — and commit. SQLAlchemy figures out what changed and writes the appropriate SQL.

Notice: one query to get the book, shared between GET and POST. In the sqlite3 version, the GET branch runs a SELECT and the POST branch runs a separate UPDATE. Here, both work on the same object.

---

## Delete

```python
@app.route("/delete/<int:book_id>", methods=["POST"])
def delete_book(book_id):
    book = db.session.get(Book, book_id)
    db.session.delete(book)
    db.session.commit()
    return redirect(url_for("list_books"))
```

---

## The template

Here's `index.html`. Compare it with the sqlite3 version:

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
      <td>{{ book.author.name }}</td>
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

One difference: `{{ book.author.name }}` instead of `{{ book.author_name }}`. The sqlite3 version used a SQL alias (`AS author_name`) from a JOIN. The ORM version walks the relationship — `book.author` is the Author object, `.name` is its attribute.

Everything else is identical. The template doesn't care whether the data came from raw SQL or an ORM. It just needs objects with the right attribute names.

---

## Bonus: things that are natural on models

Once your data lives in Python classes, you can add behaviour to those classes. This is where the ORM starts to feel genuinely different from raw SQL.

```python
class Author(db.Model):
    # ... columns as before ...

    @property
    def is_living(self):
        return self.death is None

    @property
    def book_count(self):
        return len(self.books)
```

Now your template can say:

```html
{% if author.is_living %}
  <span>Living author — {{ author.book_count }} books</span>
{% endif %}
```

With sqlite3, you'd compute this in the route and pass it separately, or write a more complex SQL query. On a model, the logic lives right next to the data it describes.

This is optional and cosmetic at small scale. It becomes significant when your models have more complex derived values — things like "is this book overdue" or "how many active projects does this student have."
