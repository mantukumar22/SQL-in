# Chapter 16 — Lab Cheat Sheet (All Labs, One Table)

Every lab/room referenced across this repo, grouped by platform, with the
chapter it supports.

## PortSwigger Web Security Academy
`portswigger.net/web-security/sql-injection`

| Lab | Difficulty | Chapter |
|---|---|---|
| SQL injection vulnerability in WHERE clause allowing retrieval of hidden data | Apprentice | 03, 04 |
| SQL injection vulnerability allowing login bypass | Apprentice | 05 |
| SQL injection UNION attack, determining the number of columns returned by the query | Apprentice | 06 |
| SQL injection UNION attack, finding a column containing text | Apprentice | 06 |
| SQL injection UNION attack, retrieving data from other tables | Practitioner | 06, 11 |
| SQL injection UNION attack, retrieving multiple values in a single column | Practitioner | 06 |
| SQL injection attack, querying the database type and version on Oracle | Practitioner | 11 |
| SQL injection attack, querying the database type and version on non-Oracle | Practitioner | 11 |
| SQL injection attack, listing the database contents on non-Oracle | Practitioner | 11 |
| SQL injection attack, listing the database contents on Oracle | Practitioner | 11 |
| Blind SQL injection with conditional responses | Practitioner | 08, 13 |
| Blind SQL injection with conditional errors | Practitioner | 08 |
| Blind SQL injection with time delays | Practitioner | 09 |
| Blind SQL injection with time delays and information retrieval | Expert | 09 |
| Blind SQL injection with out-of-band interaction | Practitioner | 10 |
| Blind SQL injection with out-of-band data exfiltration | Expert | 10 |
| SQL injection with filter bypass via XML encoding | Expert | 14 |

## TryHackMe
`tryhackme.com`

| Room | Focus | Chapter |
|---|---|---|
| SQL Injection | Fundamentals, UNION, error-based, blind | 01, 02, 04, 06, 07, 08, 09 |
| SQLMap: The Basics | sqlmap automation, `--dbs`/`--tables`/`--dump` | 12 |
| Intro to WAFs / filter-bypass rooms | Evasion | 14 |

## Self-Hosted Practice Apps
| App | Best for |
|---|---|
| DVWA (Damn Vulnerable Web App) | All techniques, adjustable security levels (Low/Medium/High/Impossible) — great for before/after prevention comparison |
| bWAPP | Broader vuln catalog including multiple SQLi variants |
| OWASP Juice Shop | Modern JS-stack app, realistic scoring/challenges |
| Metasploitable2 | Legacy vulnerable services, general pentest practice |

## Suggested Certification Alignment
- **eJPT / PNPT** — Ch. 01–09, 12–13 cover everything typically tested.
- **OSCP** — add strong manual technique fluency (Ch. 05–11) since
  automated tools are restricted in the exam environment.
- **CEH** — Ch. 01–02, 15 map closely to exam objectives on SQLi theory
  and countermeasures.

## Personal Progress Tracker
Copy this into your notes and check off as you complete each lab:

- [ ] WHERE clause hidden data retrieval (PortSwigger)
- [ ] Login bypass (PortSwigger)
- [ ] UNION — column count (PortSwigger)
- [ ] UNION — text column (PortSwigger)
- [ ] UNION — retrieve from other tables (PortSwigger)
- [ ] UNION — multiple values, one column (PortSwigger)
- [ ] DB type/version — Oracle (PortSwigger)
- [ ] DB type/version — non-Oracle (PortSwigger)
- [ ] List DB contents — non-Oracle (PortSwigger)
- [ ] List DB contents — Oracle (PortSwigger)
- [ ] Blind — conditional responses (PortSwigger)
- [ ] Blind — conditional errors (PortSwigger)
- [ ] Blind — time delays (PortSwigger)
- [ ] Blind — time delays + info retrieval (PortSwigger)
- [ ] Blind — OOB interaction (PortSwigger)
- [ ] Blind — OOB data exfiltration (PortSwigger)
- [ ] Filter bypass via XML encoding (PortSwigger)
- [ ] SQL Injection room (TryHackMe)
- [ ] SQLMap: The Basics room (TryHackMe)
- [ ] DVWA — all security levels
