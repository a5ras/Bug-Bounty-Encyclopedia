# 04: DOM-Based XSS

The last XSS family is **DOM-based XSS**. Here the bug isn't in the server's output—it's in the website's own **client-side JavaScript**. The page takes data from a **source** we control and feeds it into a dangerous **sink**, right there in the browser.

Remember the vocabulary from lesson 01: a **source** is where our data enters (very often `location.search`, the query string of the URL), and a **sink** is the function it flows into that ends up executing or rendering it.

> Reminder: **View source won't help you here.** It shows the server's original HTML, not the live DOM after JavaScript rewrote it. Use the dev-tools element inspector to see the real result.

Let's walk through three sinks.

---

## Lab A: `document.write` Sink

* **Lab:** [DOM XSS in document.write sink using source location.search](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink-using-source-location-search)

The search-tracking script reads our query string (`location.search`) and passes it into **`document.write`**, which writes data straight onto the page. Because we control the URL, we control what `document.write` emits—so we can corrupt how the page's JavaScript builds the DOM and inject our own markup to call `alert`. Goal met.

---

## Lab B: `innerHTML` Sink

* **Lab:** [DOM XSS in innerHTML sink using source location.search](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-innerhtml-sink-using-source-location-search)

This time the script takes data from `location.search` and assigns it to an element's **`innerHTML`**, changing the contents of a `<div>`.

Here's the catch worth remembering: **`innerHTML` will not execute a plain `<script>` tag.** So the usual `<script>alert(1)</script>` won't fire. But `innerHTML` does render other elements—including an `<img>` that has an event handler like **`onerror`**. If we give an image a broken source, the error handler runs our JavaScript:

```html
<img src=x onerror=alert(1)>
```

The image fails to load, `onerror` fires, and the alert pops. Lesson: when one payload is blocked, switch to a sink-appropriate one.

---

## Lab C: jQuery Anchor `href` Sink

* **Lab:** [DOM XSS in jQuery anchor href attribute sink using location.search source](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-jquery-href-attribute-sink-using-location-search-source)

This feedback page uses the **jQuery** `$` selector to grab an anchor (`<a>`) element and sets its **`href`** attribute from `location.search`. Because the link's destination is built from URL data we control, we can turn the **"back" link** into one that runs JavaScript, with the goal of making it alert `document.cookie`.

---

## Takeaway

DOM XSS all comes down to the same question: **which source reaches which sink, and what will that sink actually execute?** `document.write` and a raw `<script>`, `innerHTML` and an `<img onerror>`, a jQuery-controlled `href`—each sink needs a payload that fits it. Find the source → trace it to the sink → craft for that sink.

That's the end of the XSS module. See you in the next one.
