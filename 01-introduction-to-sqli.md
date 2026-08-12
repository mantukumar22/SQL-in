# Chapter 01 — What is SQL Injection?

## About the Topic
SQL Injection (SQLi) is a web security vulnerability that lets an attacker
interfere with the queries an application sends to its database. It has
been in the **OWASP Top 10** for over a decade because it's easy to
introduce and devastating when exploited.

**Root cause:** user-supplied input is concatenated directly into an SQL
query string instead of being treated as pure data (via sanitization or
parameterized queries).

```sql
-- Vulnerable pattern (pseudo-code)
query = "SELECT * FROM users WHERE username = '" + input + "'"
```

If `input` is attacker-controlled, the attacker can change the *meaning* of
the query, not just its data.

## Why an Ethical Hacker Cares
From an offensive standpoint, SQLi is one of the highest-value bugs you can
find because a single injection point can lead to:

| Impact | Description | Severity |
|---|---|---|
| Data Extraction | Dump usernames, password hashes, PII, card data | Critical |
| Auth Bypass | Log in without valid credentials | Critical |
| Data Manipulation | INSERT/UPDATE/DELETE records | Critical |
| Remote Code Execution | Write files / execute OS commands via the DB | Critical |
| Privilege Escalation | Access admin accounts or other tenants' data | High |
| DoS / Data Loss | DROP tables, wipe records | High |

## Practical Example
A normal login query:

```sql
SELECT * FROM users WHERE username = 'alice' AND password = 'secret123';
```

If the username field is not sanitized, an attacker submits:

```
' OR '1'='1
```

Producing:

```sql
SELECT * FROM users WHERE username = '' OR '1'='1' AND password = '';
-- '1'='1' is always TRUE -> returns all rows -> auth bypassed
```

## Labs to Practice
- **PortSwigger Web Security Academy** → *SQL Injection* topic index
  (`portswigger.net/web-security/sql-injection`) — start with "What is SQL
  injection?" reading, then the Apprentice labs.
- **TryHackMe** → *SQL Injection* room (intro-level, guided).
- **OWASP Juice Shop** → "Login Admin" challenge (score-based, self-hosted).
- **DVWA** → SQL Injection module, Security = Low, to see the raw query flow.

## Key Takeaway
Every SQLi finding starts the same way: identify a place where user input
reaches a database query, then prove you can alter the query's logic.
