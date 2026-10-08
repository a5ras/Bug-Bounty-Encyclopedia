# 01: What is Information Disclosure?

Information Disclosure, also known as "Information Leakage," is a vulnerability that occurs when a web application accidentally reveals sensitive data to its users.

The core principle of this vulnerability is **sensitive data exfiltration**—the data is leaked out and becomes accessible to **anyone**, including unauthorized users. When you, as a researcher, find sensitive data that is not meant to be public, you have found a reportable Information Disclosure vulnerability.

---

## Relation to OWASP Top 10

The **OWASP (Open Web Application Security Project)** is a non-profit organization that provides unbiased, practical information about web and software security. Their most famous project is the **OWASP Top 10**, which ranks the 10 most critical web security risks.

Information Disclosure is directly related to **A02:2021 - Cryptographic Failures**, which is the second most dangerous category. This is because a "cryptographic failure" (like not encrypting data, or using weak encryption) is one of the primary *causes* that *leads to* Information Disclosure. If a database of passwords leaks, it's an Information Disclosure vulnerability. If those passwords were also stored in plain text, it's also a Cryptographic Failure.

---

## Why Do We Start With This Vulnerability?

This vulnerability is the perfect starting point for any bug bounty hunter for two main reasons:

1.  **It's Easy to Learn and Find:** It's often considered "low-hanging fruit." You don't need complex tools or deep programming knowledge to get started.
2.  **The Methodology is Simple:** The whole process is about **reconnaissance** (searching). You are simply looking for secrets that the website is accidentally showing. This includes searching for:
    * Secret files or folders.
    * Sensitive data hidden in the page's code (HTML comments, JavaScript files).
    * Verbose error messages that leak server details.
    * Forgotten configuration files (`.git`, `.env`).

---

## The Real Impact: A Gateway Vulnerability

We can (and should) report this vulnerability as soon as we find it. But its true power is that the **exfiltrated data can help us find other, more severe vulnerabilities.**

This is called **chaining attacks**. The leaked data is a clue that leads to a bigger prize.

* **Example 1:** You find a version number (`Apache/2.4.41`). You can now search for a public exploit for that specific, old version.
* **Example 2:** You find a list of usernames in a comment. You can now use that list to perform a **brute-force** or **password-spraying** attack on the login page.
* **Example 3:** You find an error message that leaks a server path (`/var/www/uploads/`). You can now use that path in a **Path Traversal** or **File Inclusion** (LFI) attack.

This is not a "small" vulnerability. It has been found and reported in massive, high-profile companies, including **Snapchat**, **Starbucks**, and **Shopify**, often leading to large bounty payouts.

---

## Next Lesson

In the next lesson, we will use a target website to start practicing how to find these leaks. See ya.
