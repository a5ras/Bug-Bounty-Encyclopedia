# 02: Username Enumeration

Our first authentication lab is about **username enumeration**: getting the site to reveal which usernames actually exist, then brute-forcing that user's password.

* **Lab:** [Username enumeration via different responses](https://portswigger.net/web-security/authentication/password-based/lab-username-enumeration-via-different-responses)

> The lab gives us two wordlists—one of candidate usernames and one of candidate passwords. Note: the lab picks a random valid user/password combo each time it starts.

---

## Step 1: Why Enumeration Is Even Possible

The leak comes from the login logic reacting *differently* depending on whether the username exists. In pseudo-code it behaves like this:

* If the username **doesn't** exist → error says **"Invalid username"**.
* If the username **exists** but the password is wrong → error says **"Invalid password"** (or similar).

Those two different messages are the whole vulnerability. The app is basically confirming valid accounts for us.

---

## Step 2: Enumerate the Username with Intruder

1. Submit a login with any junk username/password and capture the `POST /login` request in Burp.
2. Send it to **Intruder** and mark the **username** field as the payload position.
3. Load the candidate-usernames wordlist as the payload.
4. Start the attack, then use the **Grep - Match** feature to flag responses containing "Invalid username."
5. Almost every response will say "Invalid username"—except **one**. That one username didn't trigger the message, which means it's a real account.

---

## Step 3: Brute-Force That User's Password

Now we repeat the process, but this time:
1. Fix the **username** to the valid one we just found.
2. Mark the **password** field as the payload position and load the candidate-passwords list.
3. Start the attack and watch the **status code** and **length** columns.

A successful login behaves differently from the failures: instead of a `200` with the same error page, the correct password gives an **HTTP 302 redirect** (to `/my-account`) and sets a fresh session cookie. That `302` is our winner.

---

## Step 4: Solving the Lab

Log in with the username and password you recovered, open the account page, and the lab is solved.

---

## The Subtle Variants

The same idea shows up in trickier forms, worth knowing:

* **Subtly different responses:** the error text is *almost* identical in both cases (maybe a tiny wording or punctuation difference). You have to diff the responses carefully instead of grepping for an obvious string.
* **Response timing:** the messages are identical, but the server is *slower* for valid usernames. This happens when the code only bothers to hash and check the password when the user actually exists ("quick exit" for non-existent users). By measuring response time—and sending a long password to exaggerate the gap—we can still tell real accounts apart.

The lesson: it doesn't matter if the leak is a message, a length, a status code, or a few milliseconds. **Any** consistent difference lets us enumerate. See ya.
