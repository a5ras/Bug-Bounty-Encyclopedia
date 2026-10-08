# 07: Bypassing Controls via Hidden Headers

This will be the last lab we will see in the *Broken Access Control* module. This one is a bit different and shows how a server might use hidden request headers to control access.

* **Lab:** [Authentication bypass via information disclosure](https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-authentication-bypass)
    *(Note: Although PortSwigger files this under "Information Disclosure" because of the **TRACE** method, it is a perfect example of a *Broken Access Control* vulnerability).*

---

## Step 1: Reconnaissance
1.  Access the lab. They provide us with the credentials *wiener:peter*.
2.  I'll go to "My account" and log in.
3.  Now, I'll try to access the admin panel directly by going to */admin*.
4.  As usual, access is denied.

## Step 2: Investigation with Burp
Let's use the proxy to see what's happening.

1.  I'll turn on **Burp Intercept** and try to access */admin* again.
2.  The request is caught. I'll look at it in Burp.
3.  I check the *Inspector*... I check the *Cookies*... there is **nothing**. No *admin=false*, no *roleid=1*.
4.  So what is preventing us from accessing the page?

## Step 3: Using the TRACE Method
Sometimes, other servers or proxies *between* our Burp proxy and the web server add more headers to our request. To see what the final request looks like, we can use the **TRACE** method.

* **What is TRACE?**
It's an HTTP method (like *GET* or *POST*) used for debugging. It asks the server to "echo" or "trace" the full request it received and send it back to you.

To use it, I will right-click the request in Burp (in Repeater or Intercept) and choose **"Change request method"**. I will change *GET* to *TRACE*.

**Example:**

I send this request:

```http
TRACE / HTTP/1.1
Host: your-lab-url.com
Cookie: session=...
```

The server will respond with the *full* request, including any headers that were added by intermediate proxies:

```http
HTTP/1.1 200 OK
Content-Type: message/http

TRACE / HTTP/1.1
Host: your-lab-url.com
Cookie: session=...
X-Custom-IP-Authorization: 1360.36.06.35  <-- A NEW, HIDDEN HEADER!
```

## Step 4: Exploitation

When I do this in the lab, a file downloads. When I open it in a text editor, I see the same thing: at the bottom, there is a new header I didn't send!
***X-Custom-IP-Authorization: 1360.36.06.35***

This must be the check! The server is checking for a specific IP.

1.  This IP looks fake. I can use social engineering to find the real IP, but in this case, I'm guessing the admin must access it from the server itself. The IP for that is the **localhost IP**: *127.0.0.1*.
2.  I'll go back to my original *GET /admin* request in **Burp Repeater**.
3.  I will *add* this new header to the request, but change the IP. I add the line:
    *X-Custom-IP-Authorization: 127.0.0.1*
4.  I'll click **"Send"**.

As you see, the admin panel appears!

## Step 5: Persistent Access (Solving the Lab)

But there's a problem. If I click "delete user," it fails. Why? Because that *new* request doesn't have my special header.

We must add this header to *every* request. We will use "Match and Replace" again, but in a different way.

1.  Go to **Proxy -\> Options -\> Match and Replace**.
2.  Click **"Add"** to create a new rule.
3.  Leave the **"Match"** field completely **BLANK**.
4.  In the **"Replace"** field, paste the full header:
    *X-Custom-IP-Authorization: 127.0.0.1*
5.  Make sure the "Type" of rule is set to **"Request header"**.
6.  Click **OK**.
7.  Turn **Intercept off**.

Now, Burp will automatically add this header to every single request you make. You can now browse the */admin* panel and delete users.

---

To solve the lab, just read the instructions and follow the steps one by one.
