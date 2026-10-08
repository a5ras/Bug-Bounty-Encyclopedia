# 04: Bypassing Two-Factor Authentication

Two-factor authentication (2FA) is supposed to add a second wall: even with a valid password, you still need a one-time code (often sent by email). But if the second step isn't *properly linked* to the login, we can sometimes jump straight over it.

* **Lab:** [2FA simple bypass](https://portswigger.net/web-security/authentication/multi-factor/lab-2fa-simple-bypass)

In this lab we already have the victim's full credentials (`carlos:montoya`) but not their 2FA code. We also have our own account (`wiener:peter`) to study how the flow works.

---

## Step 1: Learn the Flow with Our Own Account

First, log in as `wiener`. The 2FA code is emailed to us (the lab has an email client button), we enter it, and we land on the account page. Note the URL:

`/my-account?id=wiener`

That `/my-account` endpoint is the page that comes *after* the 2FA check. Keep it in mind.

> I first tried changing `id=wiener` to `id=carlos` here—an IDOR attempt—but that didn't work; it just logged me out and bounced me to the login page. Still worth testing. To keep things clean, I cleared all cookies so no half-authenticated session was left hanging.

---

## Step 2: The Real Bypass — Direct Browsing

Now the actual flaw:

1. Log in with the **victim's** credentials `carlos:montoya`.
2. The site now asks for the 2FA verification code.
3. Instead of entering a code, **manually change the URL** to `/my-account`.

The page loads as Carlos. Lab solved.

---

## Why This Works

The problem is that the application treats you as **authenticated the moment the username and password are correct**. The 2FA code screen is just the *next page* it shows you—but there's **no real link** between passing the 2FA check and being allowed into the account.

So if we already know the endpoint that comes after 2FA (and we do, because we mapped it with our own account), we can **browse directly to it** and skip the code screen entirely. This is often called **direct browsing** or a **direct bypass**.

---

## The Broken-Logic Variant

There's a tougher cousin of this lab, **2FA broken logic**, where the second factor is vulnerable because of *flawed logic* in how the code is generated, bound to a user, or validated—rather than a simple "just skip the page" jump. The lesson is the same at heart: a second factor only helps if it is **tightly bound** to the specific user and cannot be reached, reused, or skipped on its own.

---

## Takeaway

2FA is only as strong as its wiring. If authentication is considered "done" before the code is verified, or if the post-2FA page can be reached directly, the extra factor adds no real protection.

That wraps up the Authentication module. See you in the next one.
