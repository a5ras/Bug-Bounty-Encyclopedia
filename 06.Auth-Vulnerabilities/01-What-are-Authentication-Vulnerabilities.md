# 01: What are Authentication Vulnerabilities?

**Authentication** is how a website proves you are who you say you are—usually a username and a password, and sometimes a second step on top. If that process has a flaw, an attacker can log in as someone else, and from there everything that user can do becomes possible too.

In this module we focus on the most common authentication problems and practice them on PortSwigger labs.

---

## The Usual Login Flow

The classic flow is **password-based authentication**: you send a username and a password, the server looks them up, and if they match a stored record, you're in. Simple—but there are several ways this can go wrong.

---

## What We'll Attack

Across the labs we'll cover three big themes:

* **Username enumeration** — the site accidentally tells us *which usernames are real* before we even know the password. Once we have a valid username, we only have to crack the password.
* **Brute-force / weak protection** — the site tries to stop us from guessing passwords, but the protection has a logic flaw we can slip around.
* **Two-factor authentication (2FA) bypass** — the site adds a second step (a code by email, for example), but the way it's wired up lets us skip it.

---

## Two Useful Ideas Up Front

> **Enumeration first, brute-force second.** It's much cheaper to confirm a valid username and then only brute-force *its* password, than to guess both at the same time. The labs follow exactly this order: find the user, then crack the pass, then open the account page.

> **Any difference is a leak.** The server doesn't have to print "this user exists" for us to know it. A slightly different error message, a different response length, a different status code, or even a different *response time* is enough for us to tell real accounts apart from fake ones.

---

## The Tools

Most of this work is done with **Burp Suite**—especially **Intruder** (and the faster **Turbo Intruder** extension) to automate sending a wordlist of candidate usernames or passwords and then sorting the responses to spot the odd one out.

In the next lesson we'll start with username enumeration. See ya.
