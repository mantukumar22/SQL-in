# Chapter 13 — Tooling: Burp Suite

## About the Topic
Burp Suite is the industry-standard intercepting proxy for manual and
semi-automated web app testing. For SQLi specifically, you'll live in
three modules: **Proxy** (capture), **Repeater** (manual payload testing),
and **Intruder** (automated fuzzing/extraction).

## Core Workflow

### 1. Proxy — Capture the Request
Route browser traffic through Burp (`127.0.0.1:8080`), submit the form
with test input, and find the request in **Proxy > HTTP history**.

### 2. Repeater — Manual Payload Testing
Right-click the request → **Send to Repeater**. Edit a parameter/cookie
directly and hit **Send**, comparing responses side by side.

```
Cookie: TrackingId=kwlN9NrihULI0OcI' AND '1'='2; session=...
```
Response: no "Welcome back" text → condition is FALSE → confirms injectable
boolean-blind point (see Ch. 08 for the full technique).

Useful Repeater features:
- **Response search** (bottom search bar) — grep for a marker string like
  "Welcome back" to quickly eyeball TRUE/FALSE state.
- Multiple tabs — keep TRUE and FALSE payloads in separate tabs to compare.
- **Inspector panel** — quickly edit headers/cookies without hand-editing
  raw text.

### 3. Intruder — Automating Extraction
1. Send the confirmed-injectable request to **Intruder**.
2. Mark the injection point with `§...§` (e.g., the character position in
   a `SUBSTRING()` payload).
3. Choose attack type:
   - **Sniper** — single payload set, one position at a time (character
     brute-force).
   - **Cluster bomb** — multiple positions (e.g., position + guessed
     character), useful for full password extraction.
4. Load a payload list (character set / numbers) into the **Payloads** tab.
5. Under **Options > Grep - Match**, add your TRUE marker (e.g., "Welcome
   back") so results are auto-flagged.
6. Start the attack — filter results by the grep match column or by
   **response length**, which is often the most reliable signal.

## Practical Example — Extracting a Password Character by Character
```
Cookie: TrackingId=xyz' AND SUBSTRING(password,1,1)='§a§ AND username='administrator
```
Set the payload list to `a-z0-9`, run Sniper, and check which payload
produces the TRUE response (grep match / response length spike). Repeat
by incrementing the `SUBSTRING` position for each subsequent character.

## Offensive Tips
- Turn on **Burp AI** / built-in scanner (Pro) alongside manual testing —
  it flags likely injection points but always confirm manually before
  reporting.
- Use **Burp Collaborator** (Pro) directly from Repeater/Intruder for OOB
  SQLi confirmation (Ch. 10).
- Save your Repeater tabs/history — engagement reports need reproducible
  request/response evidence, not just "it worked."

## Labs to Practice
- **PortSwigger Academy** — every lab is designed to be solved with Burp
  Suite (Community edition is sufficient for Apprentice/Practitioner
  labs); Repeater is used for the *Blind SQL injection with conditional
  responses* lab specifically.
- **TryHackMe** — *SQL Injection* room and *SQLMap: The Basics* room both
  reference capturing requests via browser DevTools/Burp before automation.

## Key Takeaway
Burp Suite is where you *prove* an injection manually (Repeater) and then
*scale* the exploitation (Intruder) — a skillset sqlmap alone won't teach
you, especially for custom/non-standard injection contexts.
