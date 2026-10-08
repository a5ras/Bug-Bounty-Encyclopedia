# 03: Bypassing Brute-Force Protection

Good sites try to stop password guessing—blocking your IP after too many failures, locking accounts, rate-limiting. But when that protection is built on flawed logic, we can walk right around it. This lesson covers three such logic flaws.

---

## Flaw 1: IP Block That Resets on Success

* **Lab:** [Broken brute-force protection, IP block](https://portswigger.net/web-security/authentication/password-based/lab-broken-brute-force-protection-ip-block)

The site blocks our IP after a few failed logins in a row. The flaw: a **successful login resets the failure counter**. And we happen to own a valid account (`wiener:peter`), while the victim is `carlos`.

The trick is to **slip our own valid login in between the guesses**. If we log in successfully often enough, the counter never reaches the block threshold.

So we build two aligned wordlists where every few entries we insert our known-good credentials:
* A **username** list that alternates `carlos` (the victim) with `wiener` (us).
* A **password** list that alternates the candidate passwords with `peter` (our real password).

Lined up, the pattern becomes: *guess carlos, guess carlos, log in as wiener (counter resets), guess carlos...* and so on.

> **Important:** send the requests **one at a time** (single-threaded). If multiple requests hit the server out of order, the reset timing breaks and the IP gets blocked anyway.

---

## Flaw 2: Account Lock Leaks Usernames

* **Lab:** [Username enumeration via account lock](https://portswigger.net/web-security/authentication/password-based/lab-username-enumeration-via-account-lock)

Here the site locks an account after several failed attempts—but that locking behaviour itself leaks which accounts are real. Only a **real** account can get locked, so the response for a valid username changes after enough tries, while a non-existent one keeps giving the normal error.

A neat way to do this with **Turbo Intruder**: queue each candidate username several times in a row (say 5×) so real accounts hit the lock, and filter out every response that still says "Invalid username or password." Anything with a *different* response stands out—that's the account that got locked, i.e. a valid user.

---

## Flaw 3: Multiple Passwords in One Request

* **Lab:** [Broken brute-force protection, multiple credentials per request](https://portswigger.net/web-security/authentication/password-based/lab-broken-bruteforce-protection-multiple-credentials-per-request)

This one is my favourite because it sidesteps rate-limiting entirely. The login endpoint accepts **JSON**, and it turns out the `password` field can be given an **array of values** instead of a single string:

```json
{"username" : "carlos", "password" : ["123456", "password", "12345678", "qwerty", "..."]}
```

The server checks the whole list in a *single* request. One request means the rate-limiter never trips, and if any password in the array is correct we get the **302 redirect** into Carlos's account.

---

## Takeaway

Brute-force defences are only as strong as their logic. If success resets the counter, we interleave a real login. If locking is observable, it becomes an enumeration oracle. If the endpoint accepts a list, we pack all our guesses into one request. See you next lesson for 2FA.
