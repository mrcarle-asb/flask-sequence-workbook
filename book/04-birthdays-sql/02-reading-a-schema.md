---
name: Reading a Database Schema
index: 2
---

# Reading a Database Schema

Before you write any queries, you need to understand the table you're querying. A **schema** defines the structure of a database table — what columns it has, what types they hold, and what rules they enforce.

You don't write the schema for this assignment — it's already created by `init_db.py`. But you need to read and understand it.

## The birthdays table

```sql
CREATE TABLE birthdays (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    month INTEGER NOT NULL,
    day INTEGER NOT NULL
);
```

This creates a table called `birthdays` with four columns. Let's break it down.

---

## Column types

Each column has a type that tells the database what kind of data it stores:

| Type      | Stores          | Example        |
|-----------|-----------------|----------------|
| `TEXT`    | Strings         | `"Alice"`      |
| `INTEGER` | Whole numbers  | `3`, `14`      |

SQL is typed at the schema level. In Python, a variable can hold anything — `x = "hello"` today, `x = 42` tomorrow. In SQL, a column declared as `INTEGER` only holds integers. The database enforces this.

---

## Constraints

Constraints are rules the database enforces on every row:

### `PRIMARY KEY AUTOINCREMENT`

```sql
id INTEGER PRIMARY KEY AUTOINCREMENT
```

Every row gets a unique `id` number, assigned automatically. You never set this yourself — the database picks the next available number. This is how you identify a specific row: not by name (names can repeat), but by `id`.

### `NOT NULL`

```sql
name TEXT NOT NULL
```

This column cannot be empty. If you try to insert a row without a `name`, the database rejects it.

Remember the `required` attribute on HTML form inputs? Same idea, different layer. The form prevents the user from submitting empty fields. `NOT NULL` prevents the database from storing them. Belt and suspenders.

---

## Tables, rows, columns

If you think in spreadsheet terms:

| Spreadsheet | Database |
|-------------|----------|
| A sheet     | A **table** |
| A row       | A **row** (one record — one birthday) |
| A column header | A **column** (one field — name, month, day) |

The `birthdays` table has 4 columns and starts with 5 rows of sample data (Alice, Bob, Charlie, Diana, Edward).

---

## The sample data

Open `init_db.py` to see how the sample rows are inserted:

```python
sample_birthdays = [
    ("Alice", 3, 14),
    ("Bob", 7, 22),
    ("Charlie", 11, 5),
    ("Diana", 1, 30),
    ("Edward", 9, 17),
]
```

This script runs once when the Codespace starts. After that, `birthdays.db` exists as a file — just like `tasks.csv` existed as a file in Week 3. The difference is that you interact with it through SQL queries, not by opening and reading it directly.

:::alert{info}
SQL appears in IB Computer Science Paper 2. Being able to read a schema and write basic queries is exam content, not just a Flask exercise.
:::
