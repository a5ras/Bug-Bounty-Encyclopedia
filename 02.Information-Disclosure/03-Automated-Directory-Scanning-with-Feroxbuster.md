# 03: Automated Discovery with Feroxbuster

This document outlines the walkthrough for a PortSwigger lab focused on finding sensitive information using automated discovery tools like Feroxbuster.

**Lab Link:** 🔗 [Information disclosure on a debug page](https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-on-debug-page)

---

## Lab Walkthrough

### Step 1: Reconnaissance

After accessing the lab, the first step is reconnaissance. A common initial check is `/robots.txt`, but you may not find it. Not all websites use this file, and some administrators intentionally omit it to avoid giving attackers an easy map of the site.

When manual checks like `robots.txt` don't yield results, we turn to automated content discovery tools. For this lab, we will use **Feroxbuster**.

* **Feroxbuster GitHub:** 🔗 [https://github.com/epi052/feroxbuster](https://github.com/epi052/feroxbuster)

### Step 2: How Feroxbuster Works

Feroxbuster is a tool designed to brute-force directories and files on web servers. We provide it with a URL and a wordlist, and it attempts to find valid paths.

The basic command structure is:

```bash
feroxbuster -u [URL] -w [WORDLIST_PATH]
```

**Flag Explanations:**
* `--u`: Specifies the target **URL** (the lab link).
* `--w`: Specifies the **Wordlist** to use for brute-forcing.

We will use the `common.txt` wordlist from the **SecLists** repository, which is a popular collection of wordlists used in security testing.

* **SecLists `common.txt`:** 🔗 [https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/common.txt](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/common.txt)

Run the command with your lab's URL and the path to your `common.txt` file.

### Step 3: Analyze the Results

Feroxbuster will display the directories and files it finds, along with their HTTP status codes.

Look for entries with a **`200 OK`** status code, as this indicates accessible content. You must review these results to find anything sensitive that shouldn't be public.

For example, you might discover a file like `phpinfo.php`. This file contains detailed configuration information about the server's PHP environment and often includes sensitive data, such as a `SECRET_KEY`.
> **What is the `SECRET_KEY`?**
>
> A `SECRET_KEY` is a crucial security component in many web frameworks (like Django, Flask, etc.). It's a unique, random string used for cryptographic signing.
>
> * **Purpose:** It's used to sign session cookies, password reset tokens, and other security-sensitive data.
> * **Risk:** If an attacker gets this key, they can forge their own cookies and "sign" them, allowing them to impersonate any user, including administrators. It should *never* be exposed publicly. Finding it in a debug file like `phpinfo.php` is a critical vulnerability.

### Step 4: Solving the Lab

1.  Run Feroxbuster against the lab URL.
2.  Analyze the `200 OK` results.
3.  Locate the file containing the sensitive information (e.g., `phpinfo.php`).
4.  Find the `SECRET_KEY` within that file.
5.  Submit the key on the lab page to complete the challenge.
