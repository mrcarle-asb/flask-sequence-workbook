---
name: "SQLite3 vs ORM"
index: 11
---

# Two Ways to Talk to a Database

Every Flask app in this sequence stores data in SQLite. The question is how your Python code communicates with that database. There are two common approaches, and they're different enough that seeing both side by side is worth your time.

**Python's built-in `sqlite3` module** — you write SQL strings directly. You manage connections, cursors, and commits yourself. The database is a file; your code sends it text commands and gets rows back.

**Flask-SQLAlchemy** — an ORM (Object-Relational Mapper) that lets you define your database tables as Python classes. Instead of writing SQL, you call methods on objects. The library writes the SQL for you.

Neither is "better." They're different tools for different scales of problem. This section walks through the same app — a book catalogue with authors — built both ways, so you can see what each approach looks like in practice.

## What's here

- [**sqlite3 Basics**](01-sqlite3-basics) — how to build a Flask app using Python's built-in database module. Start here if your project has a simple, flat schema.
- [**Flask-SQLAlchemy Basics**](02-flask-sqlalchemy-basics) — how to build the same app using the ORM. Start here if your project has relationships between tables.
- [**Comparison**](03-comparison) — the same operations shown side by side, and a framework for choosing between them.

## The example

Both pages use the same schema: a **books** table and an **authors** table, connected by a foreign key. You'll recognize these from the Practical DB textbook (Painter-Wakefield). The data is the same; only the interface changes.

The Jinja templates are nearly identical in both versions. That's the point.
