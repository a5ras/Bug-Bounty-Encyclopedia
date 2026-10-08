# 02: Retrieving Hidden Data (WHERE Clause)

Let's do our first SQL Injection lab. The goal here is to abuse a query's `WHERE` clause so the site shows us products it normally hides.

* **Lab:** [SQL injection vulnerability in WHERE clause allowing retrieval of hidden data](https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data)

---

## Step 1: Understand the Query Behind the Page

The lab is a simple gift shop. When we pick a category, the site filters the products for us. Behind the scenes, the application is running something close to this:

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

Two things to notice in that query:
* `category = 'Gifts'` → only the category we picked.
* `released = 1` → only products that are already released (so unreleased ones stay hidden).

If we can control the `category` value, we can mess with the whole condition.

---

## Step 2: Spot the Injection Point

Look at the URL when a category is selected:

`/filter?category=Tech+gifts`

That `category` parameter is going straight into the query. Let's test it by adding a single quote right after the value:

`/filter?category=Tech+gifts'`

Our quote lands inside the string and breaks the SQL syntax. The server answers with an **HTTP 500 Internal Server Error**. A `500` here is good news for us—it usually means our input reached the query and broke it. Injection confirmed.

> I like to capture the request and replay it in **Burp Repeater**, so I can tweak the parameter and resend it quickly without reloading the browser each time.

---

## Step 3: Comment Out the Rest of the Query

The task has two parts. The easier one first: get rid of the `released = 1` condition so unreleased products also show.

We do that with a SQL comment (`--`). Everything after `--` is ignored by the database:

`/filter?category=Tech+gifts'--`

Now the query the server runs effectively becomes:

```sql
SELECT * FROM products WHERE category = 'Gifts'-- ' AND released = 1
```

The `released = 1` part is now just a comment. The page loads with a `200 OK` and we can already see products that were hidden before.

---

## Step 4: Show Every Category with Boolean Logic

Second part: we want *all* categories, not just the one we picked. For that we inject a condition that is **always true** using `OR 1=1`:

```sql
SELECT * FROM products WHERE category = 'Gifts' OR 1=1 -- ' AND released = 1
```

Because `1=1` is always true, the `WHERE` clause matches every row, so every product in every category comes back.

> **Watch the URL encoding!** Characters like the `=` need to be URL-encoded in the request, otherwise the server may reply with `400 Bad Request`. For example `1=1` becomes `1%3d1`.

---

## Step 5: Solving the Lab

Put it together, send the request with the always-true condition and the comment, and the full product list appears. Lab solved.

This first lab already shows the two building blocks we'll reuse everywhere: the **`--` comment** to cut off the rest of a query, and an **always-true condition** to break filters. See you in the next one.
