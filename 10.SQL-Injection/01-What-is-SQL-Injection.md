# 01: What is SQL Injection?

In this new module, we start with one of the oldest but still most dangerous web vulnerabilities: **SQL Injection** (often written as **SQLi**).

It's an old bug, but it's "old-but-gold." It is still behind a lot of the big data breaches we read about, and it is simple enough to be a great first topic while being serious enough to matter even for experienced hunters.

---

## The Core Idea

A website usually keeps its data (users, products, prices...) inside a **database**. When you do something on the site, the application builds a **SQL query** and sends it to that database to read or write data.

The vulnerability shows up when **our input is placed directly inside that query** without being handled safely. If the data we type becomes part of the query itself instead of staying "just data," we can change the *meaning* of the query. That's the whole trick.

So two conditions need to be true:
1. Our input reaches the application from a place the server doesn't fully trust (a URL parameter, a login field, a cookie...).
2. That input is used to build the SQL query on the fly.

When both happen, we can start injecting.

---

## How Do We Even Notice It?

The first clue is often an **error**. If we drop a single quote (`'`) into a parameter and the application suddenly throws a database error (like an HTTP `500` response), that's a strong hint the query broke because our quote landed inside it.

* If the app shows detailed error messages, our job is easier—the errors leak the shape of the original query.
* If the app hides the errors, we have to work "blind" and reconstruct the logic ourselves by observing how the page reacts.

---

## The Main Techniques

There are a few classic ways to exploit SQLi. They can also be mixed together depending on the situation:

* **UNION based:** Used when the injection is inside a `SELECT`. We glue a second query onto the first one and read its results in the same response.
* **Boolean based:** We feed the query a condition that is either true or false, and we watch the page change depending on the answer.
* **Error based:** We force the database to throw an error on purpose, because the error text itself leaks information back to us.
* **Time delay (time based):** We tell the database to "sleep" for a few seconds when a condition is true. If the response is slow, the condition was true. Useful when we get no visible output.
* **Out-of-band:** We make the database reach out over a different channel (like a DNS or HTTP request to a server we control) to send us the data.

We will meet these across the labs in this module.

---

## A Quick Word on Impact

SQL Injection can go from "see hidden products" all the way to "dump every username and password in the database" or "log in as the administrator." That's why it has such a high severity.

In the next lesson, we'll do our first lab and actually retrieve hidden data. See ya.
