# 04: Exploiting Exposed .git Folders

This walkthrough covers how to find and exploit a publicly exposed `.git` folder to access version control history and uncover sensitive information.

**Lab Link:** 🔗 [Information disclosure in version-control history](https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-in-version-control-history)

---

## Lab Walkthrough

### Step 1: Information Gathering

After accessing the lab, start by gathering information.

1.  **Manual Inspection:** Browse the website. In this lab, you will see a **Home page** and a **"My account" page**. Check them for any clues.
2.  **Check `robots.txt`:** Try accessing the `/robots.txt` file. You might find something, you might not, but it's always a good file to check.
3.  **Automated Discovery:** Use **Feroxbuster** (from our last lesson) with the lab's URL to find hidden files and directories.

```bash
feroxbuster -u [LAB_URL] -w /path/to/common.txt
```

As you find files and folders, check every one of them. Look for sensitive data like emails, notes, or anything that could help you find more vulnerabilities or even exfiltrate data. **Don't forget to add these findings to your report!**

### Step 2: Discovering the `.git` Folder

During your Feroxbuster scan, you might find a `.git` folder.

> **What is a `.git` folder?**
>
> When developers use Git (a version control system), a `.git` folder is created. This folder is a database that saves all the project's **logs and history**. It tracks every change made to the code.
>
> **Why is this a vulnerability?**
>
> This folder should *never* be public on a live website. If you can access it, you can see the entire history of the project. This means you can find sensitive data (like passwords or API keys) that was accidentally committed and later "removed." The history is still saved!

### Step 3: Exploitation

#### 3.1: Download the Folder

Once you confirm the `.git` folder exists, you can download it. A simple way is to use `wget` with the recursive (`-r`) flag.

```bash
# This command recursively downloads the entire .git directory
wget -r [LAB_URL]/.git
```

This will download the folder to your machine, likely under a directory named after the lab's URL.

#### 3.2: Analyze the History (Linux Terminal)

In a Linux terminal. Once you have the `.git` folder, `cd` into that directory. Here are the most important Git commands for finding secrets:

1.  **`git log`**: This is the most important command. It shows you a list of all the commits (changes) made to the project, from newest to oldest.

    ```bash
    git log

    # You might see output like this:
    # commit a1b2c3d4... (HEAD -> master)
    # Author: Admin <admin@example.com>
    # Date:   Mon Oct 20 10:30:01 2025
    #
    #    Remove admin password from config
    #
    # commit e5f6g7h8...
    # Author: Admin <admin@example.com>
    # Date:   Mon Oct 20 10:25:00 2025
    #
    #    Add config file
    ```
    The commit message "Remove admin password" is very suspicious!

2.  **`git show [COMMIT_HASH]`**: This command shows you *exactly* what changed in a specific commit. Let's look at the commit where the password was removed (`a1b2c3d4`).

    ```bash
    git show a1b2c3d4

    # Output will show:
    # ...
    # -   PASSWORD = "supersecret_password123"   <-- The deleted line (in red)
    # +   PASSWORD = "[REDACTED]"                <-- The added line (in green)
    ```
    You just found the password that the developer tried to remove.

3.  **`git diff [HASH_1] [HASH_2]`**: This shows the differences *between* two commits. If you want to see what changed between the "Add config" commit and the "Remove password" commit:

    ```bash
    git diff e5f6g7h8 a1b2c3d4
    ```

#### 3.3: Analyze the History (Git GUI - Alternative)

As you noted, you can also use a graphical tool.

1.  Download a tool like `git-gui`.
2.  Open the folder you downloaded.
3.  In the top menu, click the **"Repository"** button.
4.  Select **"Visualize master's History"**.
5.  This will show you a visual graph of all the commits. You can click on each one to see what changed and look for sensitive information.

### Step 4: Solving the Lab

To solve this lab, just read the instructions and follow the steps we learned. You will need to find the exposed `.git` folder, analyze its history, and find a secret that was accidentally committed.
