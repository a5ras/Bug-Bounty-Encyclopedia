# 01: What is an Injection Vulnerability?

Before we dive into the specific injection modules (SQL, OS command, and others), it's worth stepping back and seeing what they all have in common. They're different attacks, but they share **one root idea**.

---

## The Common Root

An **injection** happens when data we supply is mixed into something the application later **interprets or executes**—a database query, a shell command, and so on—without being kept safely separate. When the boundary between *"data"* and *"code/command"* breaks down, our input stops being treated as plain text and starts changing what the application actually *does*.

Two conditions tend to be present:
1. Input reaches the application from a source it doesn't fully trust (a URL parameter, a form field, a cookie, a header).
2. That input is used to build a query or command on the fly.

If both are true, there's a chance to inject.

---

## How We Detect It

The detection pattern is similar across injection types: send a character that would have **special meaning** to the interpreter and watch for something to break or behave unexpectedly.

* With SQL, a stray single quote (`'`) often triggers a database error (like an HTTP `500`). That error is a strong hint the input landed *inside* a query.
* More generally, any unexpected error, a changed response, a timing difference, or raw command output showing up on the page tells us our input is being interpreted rather than just stored.

---

## Why It Matters

Injection consistently ranks among the most serious web vulnerabilities, because the payoff is so high: reading or dumping a whole database, bypassing a login, or even running commands on the underlying server. The severity depends on *what* is being injected into and *what that interpreter can do*.

---

## What's Covered in This Encyclopedia

The reference material behind these notes gives us real, hands-on coverage of two injection families, each with its own module:

* **SQL Injection** (Module 10) — injecting into database queries.
* **OS Command Injection** (Module 08) — injecting into shell commands run by the server.

The next lesson gives a short side-by-side so the shared pattern is clear before you work through each module in depth. See ya.
