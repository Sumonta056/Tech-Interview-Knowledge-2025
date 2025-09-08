# SQL

<details>

<summary>How SQL Query Executes In a Database?</summary>

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

</details>

<details>

<summary>SQL Alias: শুধু শর্টকাট নয়, পারফরম্যান্স বুস্টার!</summary>

SELECT e.salary FROM employees AS e;\
\
❓ আমি অনেকদিন ধরে ভাবতাম — “Alias তো শুধুই শর্টকার্ট, Engine নিজেই বুঝে নেবে কাকে বলা হচ্ছে…”\
কিন্তু বাস্তবতা আরও গভীর।\
🔍 PostgreSQL যখন আপনার query পায়, তখন ৪টা ধাপে কাজ করে:\
1️⃣ Parsing\
SQL কে ভেঙে ভেঙে token বানায় — প্রতিটা keyword, table, column আলাদা করে।\
যেমন —\
EmployeeFinancialDatabase2025Backup.salary\
এখান থেকে অনেক বড় টোকেন তৈরি হয়, যেটা more time + more memory নেয়।\
অন্যদিক - e.salary\
এটা কম character, কম খরচ।

2️⃣ Validation\
প্রতিটা token valid কিনা চেক করে — spelling, quotes, syntax error এসব ধরা পড়ে এখানেই।

3️⃣ Resolution\
salary কোন table থেকে এসেছে — সেটা খুঁজে বের করে।\
যদি alias না থাকে, তখন engine কে বারবার full table name match করতে হয়।

4️⃣ Ambiguity Check\
SELECT id লিখলে — যদি ২টা টেবিলেই id থাকে, তখন PostgreSQL confuse হয় — কোনটা নিবে?

⚡ এখন দেখেন দুইটা Query:\
❌ স্লো Query:\
SELECT EmployeeFinancialDatabase2025Backup.salary\
FROM EmployeeFinancialDatabase2025Backup;

✅ ফাস্ট Query:\
SELECT e.salary\
FROM EmployeeFinancialDatabase2025Backup AS e;\
কেন দ্বিতীয়টা ফাস্টার?\
কারণ:\
✅ ছোট alias ➜ কম টোকেন ➜ কম lookup ➜ কম ambiguity ➜ বেটার পারফর্মেন্স!

🎯 শেখার পয়েন্ট:\
"Alias শুধু লেখার সুবিধা না — এটা Engine কে কম চিন্তা করিয়ে overall efficiency বাড়ায়যারা SQL শিখছেন বা PostgreSQL নিয়ে কাজ করছেন — এটা একটা subtle but important ব্যাপার।\
\
Reference: [Lutful Mahbub Mehedi](https://www.linkedin.com/in/lutful-mehedi?miniProfileUrn=urn%3Ali%3Afsd_profile%3AACoAAC7qu1gBoP3-05a5kN2_n1Mgw35pdgucvmU)

</details>
