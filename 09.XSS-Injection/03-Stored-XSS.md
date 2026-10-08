# 03: Stored XSS

**Stored XSS** is the nastier sibling of reflected XSS. Instead of bouncing back in a single response, our payload gets **saved** by the application and then served to everyone who later views that page—no special link required.

* **Lab:** [Stored XSS into HTML context with nothing encoded](https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded)

The goal is to submit a **comment** that calls `alert` whenever the blog post is viewed.

---

## Step 1: Find a Place That Stores Our Input

This lab has a blog post with a **comment** feature. Comments are a perfect target because, by design, whatever we write gets **stored and shown back** to future visitors. If the app doesn't sanitise comments, our payload becomes part of the page for everyone.

---

## Step 2: Drop the Payload Into the Comment

Same as the reflected lab, this one encodes nothing, so we just put a script into the comment body:

```html
<script>alert(1)</script>
```

Submit the comment. From now on, **every time the blog post loads**, the stored comment loads with it, and the script runs. The alert fires and the lab is solved.

---

## Why Stored Is Worse Than Reflected

Think about the difference in delivery:

* **Reflected** → the attacker has to trick each victim into clicking a crafted link.
* **Stored** → the payload sits on the server. The site itself serves it to **anyone** who opens the page, automatically, as many times as the page is viewed.

So stored XSS can hit a lot of users passively, including staff or admins who review comments—which, remember, raises the severity a lot because of their higher privileges.

---

## Takeaway

If an app saves user input and later displays it without encoding, test it with a script payload. The method is simple—**put a script where input is stored and see if it triggers on load**—but the impact can be broad. See you next lesson for DOM-based XSS.
