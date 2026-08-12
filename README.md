# SQL Injection — Offensive Security Field Guide

A chapter-wise, lab-driven reference for learning SQL Injection (SQLi) from an
ethical hacker / penetration tester perspective. Each chapter covers the
concept, why it matters, a practical (safe/sandboxed) example, and named labs
you can use to practice it hands-on.

> ⚠️ **Legal Warning**
> SQL Injection testing must **only** be performed on systems you own, have
> **written authorization** for, or on intentional practice platforms
> (PortSwigger Web Security Academy, TryHackMe, HackTheBox, DVWA, bWAPP,
> Damn Vulnerable GraphQL, OWASP Juice Shop, Metasploitable2). Unauthorized
> testing is illegal under laws such as the Computer Fraud and Abuse Act
> (CFAA) in the US, the Computer Misuse Act in the UK, and equivalent
> legislation worldwide. This repo is for authorized security research,
> bug bounty (within program scope), CTFs, and certification prep
> (OSCP/eJPT/PNPT/CEH) only.

## Chapters

| # | Chapter | Focus |
|---|---------|-------|
| 01 | [What is SQL Injection](01-introduction-to-sqli.md) | Fundamentals, OWASP ranking, root cause |
| 02 | [Types of SQL Injection](02-types-of-sql-injection.md) | In-band, Blind, Out-of-band taxonomy |
| 03 | [Finding Injection Points](03-finding-injection-points.md) | Attack surface mapping |
| 04 | [Detection & Basic Payloads](04-detection-payloads.md) | Probing for the vuln |
| 05 | [Authentication Bypass](05-authentication-bypass.md) | Login form attacks |
| 06 | [UNION-Based SQLi](06-union-based-sqli.md) | Extracting data directly |
| 07 | [Error-Based SQLi](07-error-based-sqli.md) | Leaking data via DB errors |
| 08 | [Blind SQLi — Boolean](08-blind-sqli-boolean.md) | True/false inference |
| 09 | [Blind SQLi — Time-Based](09-blind-sqli-time-based.md) | Timing inference |
| 10 | [Out-of-Band SQLi](10-out-of-band-sqli.md) | DNS/HTTP exfiltration |
| 11 | [Database Enumeration](11-database-enumeration.md) | information_schema, versions, users |
| 12 | [Tooling — sqlmap](12-tools-sqlmap.md) | Automation |
| 13 | [Tooling — Burp Suite](13-tools-burpsuite.md) | Manual + semi-automated testing |
| 14 | [WAF/Filter Evasion](14-waf-evasion.md) | Bypassing weak protections |
| 15 | [Prevention & Defense](15-prevention-defense.md) | Blue-team / secure coding |
| 16 | [Lab Cheat Sheet](16-lab-cheatsheet.md) | Every lab/room referenced, in one table |

## Suggested Learning Path

1. Read Ch. 1–4 to understand the vulnerability class and how to spot it.
2. Solve the PortSwigger **Apprentice** labs (Ch. 5–7) manually — no tools.
3. Move to **Practitioner** labs (Ch. 8–10) to master blind techniques.
4. Learn `sqlmap` and Burp Suite (Ch. 12–13) to speed up real engagements.
5. Study WAF evasion (Ch. 14) once manual/automated exploitation is solid.
6. Finish with Ch. 15 so you can also *write the fix*, not just the exploit —
   this is what separates a professional pentester from a script kiddie.

## Toolbox Referenced in This Repo
- **sqlmap** — automated SQLi detection/exploitation
- **Burp Suite** (Community/Pro) — Repeater, Intruder, Proxy
- **Browser DevTools** — Network tab for request capture
- **DVWA / bWAPP / Juice Shop** — local vulnerable apps
- **PortSwigger Web Security Academy** — free hosted labs
- **TryHackMe** — guided rooms with real VMs
