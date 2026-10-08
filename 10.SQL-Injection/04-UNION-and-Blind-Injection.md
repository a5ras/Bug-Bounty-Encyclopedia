# 04: UNION Attacks and Blind SQL Injection

In the first two labs the injection was easy to see: the page reacted right away and even showed us data. But SQL Injection comes in many shapes. In this lesson I'll map out the rest of the labs in this module and what each one is really about, so we know what we're aiming for.

---

## UNION Attacks

When the injection sits inside a `SELECT` and the query's results are printed back on the page, we can use the **UNION** operator. UNION lets us bolt a second query onto the original one and read *its* results in the same response—so we can pull data out of tables the page was never meant to show.

UNION attacks are usually done in steps, and PortSwigger splits them into separate labs:

* **Determining the number of columns** the original query returns. A UNION only works if both queries return the *same number of columns*, so this is always the first thing to figure out.
* **Finding a column that holds text**, so we have a spot to display the string data we want to steal.
* **Retrieving data from other tables**—for example reading a `users` table that has `username` and `password` columns, then logging in as administrator with what we find.
* **Retrieving multiple values in a single column** when only one column is usable.

---

## Examining the Database

Different database engines (Oracle, MySQL, Microsoft SQL Server...) behave differently, so it helps to fingerprint what we're dealing with. A couple of labs focus on this:

* Reading the **database type and version string** (the method differs between Oracle and MySQL/Microsoft).
* **Listing the database contents**—figuring out the names of the tables and their columns, then dumping the one that holds the credentials. Again, the exact approach changes between Oracle and non-Oracle databases.

---

## Blind SQL Injection

Things get harder when the query runs but its results are **never shown** and errors are hidden. This is **blind** SQLi, and we have to *infer* the answers instead of reading them. In these labs the injection point is a **tracking cookie** the app uses for analytics. The different flavours:

* **Conditional responses:** the results aren't returned, but the page shows something small (like a "Welcome back" message) only when the query returns a row. We flip that message on and off to read data one piece at a time.
* **Conditional errors:** the page looks identical whether rows come back or not, but a crafted query that *errors* makes the app show a custom error page. That error becomes our true/false signal.
* **Time delays:** no visible difference at all, so we make the database **pause** for a number of seconds when a condition is true. A slow response means "true." One lab just asks us to trigger a 10-second delay; another uses the same idea to read out the administrator's password character by character.
* **Out-of-band (OOB):** the query runs asynchronously and doesn't affect the response we see. Here we make the database open a connection to an **external domain we control** (Burp Collaborator). One lab only asks for a **DNS lookup**; a tougher one uses that same channel to **exfiltrate data** out of the database.

---

## Takeaway

The detection trick is the same everywhere—slip in something that changes the query and watch what happens. What changes is **how we read the answer**: directly on the page, through a true/false signal, through timing, or through an out-of-band channel. Once you can read the answer in *some* way, you can walk the whole database.

That wraps up SQL Injection. See you in the next module.
