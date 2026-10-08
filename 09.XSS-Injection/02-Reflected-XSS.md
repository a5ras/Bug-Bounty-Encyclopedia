# 02: Reflected XSS

Let's start with the simplest flavour: **reflected XSS**, where our input is echoed straight back into the page with no cleaning at all.

* **Lab:** [Reflected XSS into HTML context with nothing encoded](https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded)

The goal, like most XSS labs, is to make the page call `alert`.

---

## Step 1: Find Where Input Is Reflected

This lab has a **search** box. When we search for a word, the site shows our word back on the results page (something like *"0 search results for: <our word>"*). That echo is the thing we want to test—our input is being reflected into the HTML.

---

## Step 2: Test If It's Really Vulnerable

"Nothing encoded" means exactly what it sounds like: the app does **no input validation, no encoding, no filtering**. It just drops whatever we typed straight into the page. So if we search for a `<script>` tag, the browser won't treat it as text—it'll treat it as real HTML and run it.

We search for a classic payload:

```html
<script>alert(1)</script>
```

Because the tag is placed into the HTML context untouched, the browser executes it and the alert fires. Done—the lab is solved.

---

## Step 3: Why This Is a Real Attack, Not Just a Popup

Here's the important part. Since the payload is reflected from the **URL**, the whole attack lives in a link. The search request looks something like:

`/?search=<script>alert(1)</script>`

That means we can **send this link to anyone**. When the victim clicks it, the script runs in *their* browser, inside the trusted site. This is exactly how reflected XSS gets used in **phishing**: craft the malicious link, dress it up, and get a target to click.

> So an `alert(1)` in a lab is a stand-in. In the real world that same spot could run code to grab the victim's session or act as them.

---

## Takeaway

Reflected XSS = unsanitised input bounced back in one response, delivered through a crafted URL. The fix on the defensive side is to **encode output** and validate input so a `<script>` tag is shown as harmless text instead of being executed.

Next lesson: stored XSS, where we don't even need the victim to click a special link. See ya.
