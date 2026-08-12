# Chapter 08 — Blind SQL Injection (Boolean-Based)

## About the Topic
"Blind" means the query results are **not** returned in the response — no
errors, no extra data on the page. Instead, you infer answers by observing
a behavioral difference (page content, HTTP status, redirect target,
element presence) between a TRUE and a FALSE injected condition.

## Practical Example
Testing a `TrackingId` cookie value one character at a time:

```
...xyz' AND '1'='1     -> "Welcome back" message shown  (TRUE)
...xyz' AND '1'='2     -> "Welcome back" message NOT shown (FALSE)
```

Because you can control the truth value of any injected condition, you can
extract data **one bit/character at a time**:

```sql
-- Confirm a table exists
' AND (SELECT 'a' FROM users LIMIT 1)='a

-- Confirm admin's password starts with 'a'
' AND (SELECT SUBSTRING(password,1,1) FROM users
       WHERE username='administrator')='a

-- Binary search a character's ASCII value (faster than brute force)
' AND (SELECT ASCII(SUBSTRING(password,1,1)) FROM users
       WHERE username='administrator') > 109
```

Repeating this per-character (or via binary search) eventually reconstructs
the full password string.

## Burp Suite Workflow
1. Send the request to **Repeater**.
2. Modify the cookie/parameter, appending `' AND '1'='1` vs `' AND '1'='2`.
3. Compare response length / "Welcome back" presence between the two.
4. Once confirmed, move to **Intruder** with a cluster-bomb or sniper attack
   to automate character-by-character extraction, using the response
   length/grep-match as the success condition.

## Offensive Tips
- Automate this — manual boolean-blind extraction is extremely slow.
  `sqlmap --technique=B` does exactly this loop for you (see Ch. 12).
- Use `SUBSTRING`/`ASCII` with binary search (test `> 64`, then `> 96`,
  etc.) instead of brute-forcing every character — cuts requests
  dramatically (log2(256) ≈ 8 requests per character vs ~95).
- Confirm the oracle is stable before scripting — flaky apps (session
  timeouts, rate limits) will corrupt your extraction.

## Labs to Practice
- **PortSwigger Academy** — *Blind SQL injection with conditional
  responses* (uses `TrackingId` cookie, exactly as shown in the Burp
  Repeater screenshot workflow above).
- **PortSwigger Academy** — *Blind SQL injection with conditional errors*.
- **TryHackMe** — *SQL Injection* room, Blind SQLi task.
- **DVWA / bWAPP** — Blind SQLi modules.

## Key Takeaway
Boolean-blind SQLi proves that "no visible output" does not mean "not
exploitable" — any observable TRUE/FALSE difference is a full data channel.
