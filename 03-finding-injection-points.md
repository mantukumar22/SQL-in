# Chapter 03 — Finding Injection Points

## About the Topic
Before you can exploit SQLi, you must map every place user input touches a
backend query. This is reconnaissance/attack-surface mapping — the step
most beginners rush past.

## Where to Look (Attack Surface)

| Surface | Example | Notes |
|---|---|---|
| GET/POST parameters | `?id=1`, `?category=shoes`, login forms, search boxes | Most common |
| HTTP headers | `User-Agent`, `Referer`, `X-Forwarded-For`, `Cookie` | Often missed by devs |
| JSON / XML bodies | API endpoints passing data to backend SQL | Common in REST APIs |
| Path parameters | `/user/1/profile`, `/product/42` | Numeric IDs in URLs |
| Cookie values | Session/tracking cookies used in queries | e.g., `TrackingId` |
| Sort/order-by parameters | `?sort=price` | Often unsanitized because "it's just a column name" |
| File upload metadata | Filename, EXIF fields stored in DB | Less common, high impact |

## Practical Example — Capturing the Full Request
1. Open browser DevTools → **Network** tab.
2. Submit the form (login, search, filter) with test data.
3. Click the request → copy the full URL/params/cookies.
4. Feed that raw request into Burp Repeater or sqlmap (`-r request.txt`).

```
GET /filter?category=Gifts HTTP/2
Host: target.web-security-academy.net
Cookie: TrackingId=kwlN9NrihULI0OcI; session=...
```

Every one of `category`, `TrackingId`, and `session` is a candidate
injection point — test each independently.

## Offensive Tips
- Don't only test the "obvious" search box — test **every** parameter,
  including ones the app doesn't visibly use for search (sort, filter,
  pagination, locale, currency).
- Headers like `X-Forwarded-For` are injected into logging/analytics
  queries surprisingly often.
- Use Burp's **Logger** / **Target > Site map** to passively collect every
  parameter the app touches during normal browsing, before you start
  actively testing.

## Labs to Practice
- **PortSwigger Academy** lab: *SQL injection vulnerability in WHERE clause
  allowing retrieval of hidden data* — practice spotting the `category`
  parameter as the injection point.
- **TryHackMe** — *SQL Injection* room, "Finding injection points" task.
- **DVWA** — compare Low vs Medium security to see how filtering changes
  where you can inject.

## Key Takeaway
Enumeration beats guessing. A methodical parameter-by-parameter sweep finds
injection points that quick manual testing misses.
