# 02: Path Traversal - Bypassing with Traversal Sequences

Let's make this a little bit harder. We're going to use another lab with the same goal—find the */etc/passwd* file—but this one is harder than the last.

* **Lab:** [File path traversal, simple case](https://portswigger.net/web-security/file-path-traversal/lab-simple)

---

## Step 1: Reconnaissance
Read the instructions, click "Access the Lab," and you are in. Start your reconnaissance. Check all the fields where the vulnerability could appear.

When we were doing the recon, we see that, same as last time, an image file is loaded and the request is caught in Burp.

## Step 2: Finding the Vulnerability
Let's send this request to **Burp Repeater** and try to change the image name to another valid image. It works.

Now, let's do the same thing as the last lab and try to get */etc/passwd*.
`GET /image?filename=/etc/passwd`

Oh oh... it doesn't work.

This means the server has some security. There are two main ideas why this failed:
1.  The server might be checking that the *filename* parameter must be in a specific folder (like */var/www/images/*).
2.  The server might be preventing us from accessing the root folder (*/)* directly.

## Step 3: The Traversal Sequence (`../`)
Let's try a new method. The server is expecting a path like this:
*/var/www/images/22.jpg*

Our target file is here:
*/etc/passwd*

We need to "traverse" *up* from the web folder and then *down* into the *etc* folder. We do this using the *../* sequence, which means "go up one directory."

If our starting point is */var/www/images/*, we need to go up three times:
1.  `../` -> takes us to */var/www/*
2.  `../` -> takes us to */var/*
3.  `../` -> takes us to */* (the root)

From the root, we can then go down to */etc/passwd*.

## Step 4: Exploitation
Instead of writing */etc/passwd*, let's use the traversal sequence. We'll add a few more `../` just to be sure we get to the root.

In Burp Repeater, I'll change the request line to:
`GET /image?filename=../../../etc/passwd`

Success! It works. The contents of the password file appear in the response.

When we have a problem like this, we can use this method. In the coming labs, we will see other, more complex methods to bypass security.

---

### Solving the Lab
To solve the lab, just follow the steps and read the instructions.
