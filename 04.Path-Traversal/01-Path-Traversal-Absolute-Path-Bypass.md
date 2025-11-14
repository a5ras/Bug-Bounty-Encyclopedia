# 01: Path Traversal - Absolute Path Bypass

In this new part of the formation, we're going to talk about a vulnerability called **Path Traversal**.

We know that any vulnerability that lets a user *see* or *modify* something over their privileges is a form of **Broken Access Control**. Path Traversal is a perfect example of this.

The idea of this vulnerability is that the bug hunter (or hacker) can access folders and files *inside the server's system*, but *outside* of the website's intended folders.

We know that a website's files are on a computer we call a server. The server has the website files, but it also has other system files that are not linked to the website.

* **Example (Linux):**
    * The website files might be in: */var/www/html*
    * The server's system files are in places like: */etc* (for configuration), */home* (for user directories), or */* (the root of the whole system).

If I can "traverse" from the web folder and read a file from */etc*, then I have a Path Traversal vulnerability.

---

## Lab: Absolute Path Bypass

Let's go to the lab.

* **Lab:** [Absolute path bypass](https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass)

### Step 1: Reconnaissance
Click "Access the Lab" after you read the instructions. Now we must do reconnaissance to find where the vulnerability is.

### Step 2: Finding the Vulnerability
While checking the pages and intercepting requests with Burp Suite, we see that when an image is loaded, the request for it appears in Burp (e.g., *GET /image?filename=some-image.jpg*). This request looks interesting.

### Step 3: Exploitation with Repeater
Let's see if we can manipulate this *filename* parameter.

1.  I'll send one of the image requests to **Burp Repeater**.
2.  In Repeater, I'll test if I can access a *file* instead of an *image*.
3.  This lab is about *absolute* paths, which means we provide the *full* path from the root (*/)* of the server. Let's try to get the Linux password file: */etc/passwd*.
4.  I'll change the request line to:
    `GET /image?filename=/etc/passwd`
5.  I'll click **"Send"**.

### Step 4: Solving the Lab
Ahah, great! It works! The response body now shows the contents of the */etc/passwd* file instead of an image.

To solve the lab, just follow the steps, read the instructions, and use this technique to get the file contents.
