# Chapter 15 — Prevention & Defense

## About the Topic
A professional offensive tester should always be able to explain *the fix*,
not just the exploit. This chapter covers what to recommend in a pentest
report, and what to check for when validating a remediation.

## Primary Defense: Parameterized Queries (Prepared Statements)
The only reliable fix — user input is always treated as **data**, never as
executable SQL, regardless of its content.

```python
# Vulnerable (string concatenation)
query = "SELECT * FROM users WHERE username = '" + user_input + "'"

# Fixed (parameterized / prepared statement)
cursor.execute("SELECT * FROM users WHERE username = %s", (user_input,))
```

```java
// Java (PreparedStatement)
PreparedStatement stmt = conn.prepareStatement(
    "SELECT * FROM users WHERE username = ?");
stmt.setString(1, userInput);
```

```php
// PHP (PDO)
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = :username");
$stmt->execute(['username' => $userInput]);
```

## Defense-in-Depth (Secondary Layers)
| Layer | Control | Notes |
|---|---|---|
| Input validation | Allow-list expected formats (e.g., numeric IDs) | Not a substitute for parameterization |
| Least privilege | DB account used by the app has minimal rights | Limits blast radius even if bypassed |
| ORM usage | Use frameworks (SQLAlchemy, Hibernate, Eloquent) correctly | Misuse (raw queries) reintroduces risk |
| Stored procedures | Only if they themselves avoid dynamic SQL | Poorly written procs can still be vulnerable |
| WAF | Blocks known attack patterns | Bypassable (Ch. 14) — not a primary control |
| Error handling | Generic error messages to users, detailed logs server-side | Prevents error-based leakage (Ch. 07) |
| Escaping (last resort) | DB-specific escaping functions | Error-prone; use only if parameterization is impossible |

## Practical Example — Reviewing Code as a Pentester
When reviewing source during a white-box engagement, flag any of these
patterns immediately:
```python
# 🚩 String formatting into SQL
query = f"SELECT * FROM products WHERE id = {product_id}"

# 🚩 String concatenation
query = "SELECT * FROM products WHERE id = " + product_id

# 🚩 .format() into SQL
query = "SELECT * FROM products WHERE id = {}".format(product_id)

# ✅ Parameterized — safe
query = "SELECT * FROM products WHERE id = %s"
cursor.execute(query, (product_id,))
```

## Report-Writing Tips
- Always cite the **specific** injectable parameter, the exact payload
  used, and the observed evidence (screenshot, response diff, timing).
- Recommend parameterized queries as the **primary** fix; list WAF/input
  validation as supplementary controls only.
- Where possible, retest after the fix is deployed to confirm the same
  payload now fails safely (generic error, no behavioral difference).

## Labs to Practice
- **PortSwigger Academy** — "How to prevent SQL injection" reading page.
- **OWASP Cheat Sheet Series** — *SQL Injection Prevention Cheat Sheet*.
- **DVWA** — compare the "Impossible" security level source code against
  "Low"/"Medium" to see parameterized queries implemented correctly.

## Key Takeaway
Offense informs defense: understanding exactly how SQLi is exploited is
what makes your remediation advice specific, actionable, and credible.
