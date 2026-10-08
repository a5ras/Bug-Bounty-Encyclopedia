# 01: What is Cross-Site Scripting (XSS)?

**Cross-Site Scripting** (XSS) is a vulnerability where an attacker gets their own **JavaScript** to run inside another user's browser, in the context of a trusted website. The site's own code runs it as if it belonged there, so our script inherits whatever that page is allowed to do.

In the labs we prove the bug by making the page call the `alert` function—if `alert` pops, arbitrary JavaScript runs, and that's the whole point.

---

## Why It's Dangerous

Once our script runs in the victim's session, we can do things like steal their cookies, read data off the page, or act on their behalf.

> **Severity scales with the victim's privileges.** The same XSS is far more serious if it lands in an **administrator's** browser than in a random guest's. Who you hit matters as much as the bug itself.

---

## The Three Types

XSS is usually split into three families, based on *where the payload lives* and *how it reaches the victim*:

* **Reflected XSS** — our input is bounced straight back in the response of a single request (for example a search term echoed on the results page). The payload travels in the **URL**, so we have to get the victim to click a crafted link. Common in phishing.
* **Stored XSS** — our payload is **saved** by the application (a comment, a profile field...) and then served to *everyone* who views that page later. No special link needed; the site hands the payload out by itself.
* **DOM-based XSS** — the vulnerability is in the site's **client-side JavaScript**. The page takes data from a **source** we control (like the URL) and passes it into a dangerous **sink** (a function that writes to the page), all in the browser—sometimes without the server ever being involved.

---

## Two Words You'll See a Lot

* **Source** — where the attacker-controlled data comes from (e.g. `location.search`, i.e. the query string in the URL).
* **Sink** — the dangerous function the data flows *into* that causes it to be executed or rendered (e.g. `document.write`, `innerHTML`). I think of the sink as the **delivery method**.

The core of DOM XSS is: *a source feeds a sink without proper sanitising.*

---

## A Quick Note on the DOM

For DOM XSS, remember that the browser's **"View source"** shows the *original* HTML from the server—it does **not** reflect changes JavaScript made to the live DOM afterwards. To see what's really happening, use the browser's developer tools / element inspector, not view-source.

Next lesson we start with the simplest case: reflected XSS. See ya.
