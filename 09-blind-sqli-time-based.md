# Chapter 09 — Blind SQL Injection (Time-Based)

## About the Topic
Used when there's **no visible difference at all** between true/false
conditions — no error, no content change, no status code difference. You
instead force the database to pause execution for N seconds *only when a
condition is true*, and measure response time.

## Time-Delay Payloads by DBMS

| DBMS | Unconditional delay |
|---|---|
| Oracle | `dbms_pipe.receive_message(('a'),10)` |
| Microsoft SQL Server | `WAITFOR DELAY '0:0:10'` |
| PostgreSQL | `SELECT pg_sleep(10)` |
| MySQL | `SELECT SLEEP(10)` |

## Conditional Time Delay
```sql
-- MySQL: delay only if condition is TRUE
' AND IF(1=1, SLEEP(5), 0)--

-- Extract data one character at a time via conditional delay
' AND IF((SELECT SUBSTRING(password,1,1) FROM users
          WHERE username='administrator')='a', SLEEP(5), 0)--
```

If the response takes ~5 seconds longer, the condition is TRUE; if it
returns immediately, it's FALSE. Repeat per character/position, exactly
like boolean-blind but using **time** as the oracle instead of content.

## Practical Example — Confirming the Injection
```
GET /filter?email=test AND SLEEP(5)
```
- Response returns in ~5s → parameter is injectable, time-based blind.
- This matches how sqlmap's `--technique=T` auto-detects: it sends a
  baseline request, then a delayed payload, and compares response times
  against a calculated threshold.

## Offensive Tips
- Time-based is the **slowest** technique — only use it when boolean/error
  based options are unavailable. Automate with sqlmap rather than by hand.
- Network jitter can produce false positives — always run a control
  request (`SLEEP(0)` or no delay) to baseline normal response time first.
- Stacked-query time delays (`; WAITFOR DELAY '0:0:5'--`) work on MSSQL
  when the driver allows multiple statements per query — useful beyond
  SELECT-only contexts.

## Labs to Practice
- **PortSwigger Academy** — *Blind SQL injection with time delays*, and
  *...with time delays and information retrieval*.
- **TryHackMe** — *SQL Injection* room, time-based blind task.
- **TryHackMe** — *SQLMap: The Basics* room (automates this technique
  end-to-end against a live AttackBox target).

## Key Takeaway
Time-based blind SQLi is the technique of last resort for silent injection
points — reliable but slow, so always automate it (Ch. 12) rather than
manually watching a stopwatch.
