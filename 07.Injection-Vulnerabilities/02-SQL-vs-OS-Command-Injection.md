# 02: SQL vs OS Command Injection

Same root idea—untrusted input ending up inside something the server interprets—but the *interpreter* is different. Here's a short comparison of the two injection families we practise in this encyclopedia, so you know what you're walking into before the dedicated modules.

---

## SQL Injection

* **What gets injected into:** a **database query** (SQL).
* **Typical entry point:** a parameter that filters or looks up data—like a product category filter, or a login form.
* **What goes wrong:** our input becomes part of the SQL statement, so we can change its logic—reveal hidden rows, bypass a password check, or read other tables entirely.
* **Signature detection:** a single quote (`'`) breaking the query and producing a database error / `500` response.
* **Where to learn it:** **Module 10 — SQL Injection**, where we cover retrieving hidden data, login bypass, UNION attacks, and blind techniques.

---

## OS Command Injection

* **What gets injected into:** a **shell command** the server runs on its operating system.
* **Typical entry point:** a feature that quietly shells out using values we supply. In the reference lab it's a **product stock checker** that runs a shell command built from user-supplied **product and store IDs**.
* **What goes wrong:** because our input is part of the command line, we can get the server to run **our** commands. That lab returns the **raw output of the command right in the response**, and the objective is to run `whoami` to reveal which user the server is running as.
* **Why it's severe:** running commands on the host is about as deep as it gets—it can lead to full control of the server.
* **Where to learn it:** **Module 08 — OS Command Injection**.

---

## The Shared Lesson

Whether the interpreter is a database or a shell, the defence is the same principle: **never build a query or command by gluing untrusted input into it.** Keep data as data (parameterised queries, safe APIs, strict validation) so our input can't cross over into the command itself.

With the big picture in mind, head into the individual modules to see each one in action.
