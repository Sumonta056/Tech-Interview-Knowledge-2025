---
icon: server
---

# Database: SQL

<details>

<summary>How SQL Query Executes In a Database?</summary>

<figure><img src=".gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

</details>

<details>

<summary>SQL Alias: শুধু শর্টকাট নয়, পারফরম্যান্স বুস্টার!</summary>

SELECT e.salary FROM employees AS e;\\

\
❓ আমি অনেকদিন ধরে ভাবতাম — “Alias তো শুধুই শর্টকার্ট, Engine নিজেই বুঝে নেবে কাকে বলা হচ্ছে…”\
কিন্তু বাস্তবতা আরও গভীর।\
🔍 PostgreSQL যখন আপনার query পায়, তখন ৪টা ধাপে কাজ করে:\
1️⃣ Parsing\
SQL কে ভেঙে ভেঙে token বানায় — প্রতিটা keyword, table, column আলাদা করে।\
যেমন —\
EmployeeFinancialDatabase2025Backup.salary\
এখান থেকে অনেক বড় টোকেন তৈরি হয়, যেটা more time + more memory নেয়।\
অন্যদিক - e.salary\
এটা কম character, কম খরচ।

2️⃣ Validation\
প্রতিটা token valid কিনা চেক করে — spelling, quotes, syntax error এসব ধরা পড়ে এখানেই।

3️⃣ Resolution\
salary কোন table থেকে এসেছে — সেটা খুঁজে বের করে।\
যদি alias না থাকে, তখন engine কে বারবার full table name match করতে হয়।

4️⃣ Ambiguity Check\
SELECT id লিখলে — যদি ২টা টেবিলেই id থাকে, তখন PostgreSQL confuse হয় — কোনটা নিবে?

⚡ এখন দেখেন দুইটা Query:\
❌ স্লো Query:\
SELECT EmployeeFinancialDatabase2025Backup.salary\
FROM EmployeeFinancialDatabase2025Backup;

✅ ফাস্ট Query:\
SELECT e.salary\
FROM EmployeeFinancialDatabase2025Backup AS e;\
কেন দ্বিতীয়টা ফাস্টার?\
কারণ:\
✅ ছোট alias ➜ কম টোকেন ➜ কম lookup ➜ কম ambiguity ➜ বেটার পারফর্মেন্স!

🎯 শেখার পয়েন্ট:\
"Alias শুধু লেখার সুবিধা না — এটা Engine কে কম চিন্তা করিয়ে overall efficiency বাড়ায়যারা SQL শিখছেন বা PostgreSQL নিয়ে কাজ করছেন — এটা একটা subtle but important ব্যাপার।\
\
Reference: [Lutful Mahbub Mehedi](https://www.linkedin.com/in/lutful-mehedi?miniProfileUrn=urn%3Ali%3Afsd_profile%3AACoAAC7qu1gBoP3-05a5kN2_n1Mgw35pdgucvmU)

</details>

<details>

<summary>Database Indexing</summary>

Data Indexing : The power of mapping

I added one line to the query:

```
WHERE user_verified = true
```

**Query performance doesn’t scale linearly.** A query that works with 1000 rows might die with 1 million. You can’t trust local testing.

In development, we had 500 test users. Query took 50ms. Fast enough that I never noticed.

In staging, we had 100,000 users. Query took 800ms. Slow, but not breaking anything. I never checked.

In production, with real data and real traffic, that query became a table scan on 12 million rows.

Here comes the power of index

## Database Indexing — Simple Explanation

### What is an Index?

Think of it like a **book's index page**. Instead of reading every page to find "recursion", you flip to the index, see "recursion → page 142", and go directly there.

A database index works the same way — it's a separate data structure that lets the database **jump directly** to matching rows instead of scanning everything.

### Your Scenario: 12M Users, No Index on `user_verified`

#### ❌ Before Index — Full Table Scan

sql

\`SELECT \* FROM users WHERE user\_verified = true;

```

What the database does internally:
```

Row 1: user\_verified = false ❌ skip Row 2: user\_verified = true ✅ keep Row 3: user\_verified = false ❌ skip ... Row 12,000,000: user\_verified = true ✅ keep\`

It checks **every single row** — all 12 million — even though only 8 million match. This is called a **Full Table Scan**.

**Result: Slow. \~2–8 seconds on a busy server.**

#### ✅ After Index — Direct Lookup

sql

\`CREATE INDEX idx\_user\_verified ON users(user\_verified);

```

Now the database builds a structure like this behind the scenes:
```

Index on user\_verified: false → \[row 1, row 3, row 7, ... 4M pointers] true → \[row 2, row 5, row 6, ... 8M pointers]\`

When you query:

sql

`SELECT * FROM users WHERE user_verified = true;`

The database goes to the index, finds `true`, and follows the **8M direct pointers** — no scanning of false rows at all.

**Result: Fast. \~50–200ms.**

### More Realistic Example — High Selectivity (Best case for indexes)

sql

* `- Finding one specific user by email (1 match out of 12M)SELECT FROM users WHERE email = 'mridul@example.com';`

|              | No Index   | With Index              |
| ------------ | ---------- | ----------------------- |
| Rows checked | 12,000,000 | \~1 (via B-Tree lookup) |
| Time         | \~5s       | \~1ms                   |

This is where indexes **shine** — highly selective columns like `email`, `username`, `phone`.

### Composite Index — Multiple Columns

sql

* `- Query: verified users from BangladeshSELECT FROM users WHERE user_verified = true AND country = 'BD';`

Create a composite index:

sql

`CREATE INDEX idx_verified_country ON users(user_verified, country);`

Now both conditions use the index together. **Order matters** — put the most selective column first.

### The Trade-off (Nothing is Free)

|                                          | Benefit       | Cost               |
| ---------------------------------------- | ------------- | ------------------ |
| **READ** queries                         | ⚡ Much faster | —                  |
| **WRITE** queries (INSERT/UPDATE/DELETE) | —             | 🐢 Slightly slower |
| **Storage**                              | —             | Extra disk space   |

Every time you insert a new user, the database must **update the index too** — so writes get a small overhead.

### When to Add an Index

✅ **Add index when:**

* Column appears in `WHERE`, `JOIN ON`, `ORDER BY` often
* Column has high cardinality (many unique values — like email)
* Table has 100k+ rows

❌ **Skip index when:**

* Small tables (full scan is fine)
* Column rarely used in queries
* Column updated very frequently (write-heavy)

### Quick Summary

> An index is a **pre-built lookup structure** that trades a little extra storage and write cost for dramatically faster reads — turning an O(n) full scan into an O(log n) or even O(1) lookup.

In your case — querying 8M verified users out of 12M without an index meant the database was doing **4 million unnecessary row checks** every single time that query ran.

</details>
