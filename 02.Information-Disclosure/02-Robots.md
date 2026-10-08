# 02: robots.txt

As I said, we will use the website **PortSwigger** to practice on these vulnerabilities. This lab is a classic example of Information Disclosure.

* **Lab Link:** [Information disclosure in robots.txt](https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-via-backup-files)

---

## Lab Walkthrough

### Step 1: Access the Lab
When you are on the webpage for the lab, click on **"Access Lab"**. This is going to open the lab for you.

### Step 2: Initial Reconnaissance
As you will see, it's a website. Now you should search this website and check the pages that it has to gather info and search for sensitive data.

* **Note:** When you click each time on a product to view its page, you will see that each product has a different `id`. Note this. We won't use it in this lesson, because now I need to find pages, files, folders, or sensitive data.

### Step 3: Searching for Hidden Folders
So now, I'm going to the **URL** and will try to search for folders or files manually. For example:
* `/passwords`
* `/uploads`
* `/usernames`
* And So On

We can use some tools to do that, but in this lesson, we will see a file named **`robots.txt`**.

### Step 4: Finding and Using `robots.txt` 📄
This `robots.txt` file is used to tell crawlers (like Google's) that they are not authorized to get some pages.

1.  Go to the URL and write `/robots.txt` at the end (e.g., `[your-lab-url]/robots.txt`).
2.  You will see a list of pages that the website doesn't want to show to people.

### Step 5: Finding the Vulnerability
Now, go search for that folder or those files listed in `robots.txt`.

If you find code (Java, PHP), or files with usernames, passwords, database details... **this is the vulnerability**.

This is how we use the `robots.txt` file for reconnaissance.

### Solving the Lab
To solve the lab, read the lab instructions and go ahead. I think you are close to the solution if you got all of this.
