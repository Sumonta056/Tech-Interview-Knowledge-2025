---
icon: boombox
---

# Road to System Design

#### **Learn the vocabulary (system design terms)**

You don't need to design systems yet. You need to not look confused when seniors talk. Here's a practical way to learn the vocabulary: match each term to a thing you've already seen.

Go through this list and for each term, write one sentence about where you've seen it in your own work&#x20;

```
Load balancer    → spreads traffic across multiple servers
                   "Nginx on my EC2 instance is doing basic load balancing"

Cache            → stores results so you don't recompute them
                   "MongoDB queries that run on every page load are candidates for caching"

CDN              → serves static files from servers near the user
                   "My Nuxt frontend on Vercel — Vercel's edge network IS a CDN"

Message queue    → lets services communicate without waiting on each other
                   "I haven't used this yet, but it would help if Spring Boot needed to
                    send emails without blocking the API response"

Rate limiting    → prevents one user from hammering your API
                   "DN Media Group's API probably needs this for public endpoints"

Health check     → a simple endpoint that says 'I'm alive'
                   "Spring Boot Actuator's /actuator/health is a health check"
```

The goal isn't to memorize definitions. It's to connect abstract terms to concrete things you've touched. That's how vocabulary actually sticks.

***

#### Basic Plan :&#x20;

* Day 1: Explain cache to a non-tech friend in 5 sentences and one drawing.
* Day 2: Netflix Play path (6 boxes). Write one fallback.
* Day 3: Twitter Home path (6 boxes). Add “celebrity vs normal user” split.
* Day 4: Loan EMI reminder flow. Add a fair grace rule.
* Day 5: Do napkin math for one feature (peak rps, storage/day).
* Day 6: Mock with a friend who interrupts you.
* Day 7: Watch your recording once. Fix two things (don’t aim for perfect).
* Day 8: Add failures: cache down, gateway timeout, hot partition.
* Day 9: Write a one-page design (any feature) with goal, trade-offs, numbers, and rollback.
* Day 10: Rest. Re-read notes for 10 minutes. Repeat the cycle.





#### Useful Resources :&#x20;

* Interview System Design Practice: [https://www.designgurus.io/learn/interview-preparation-guide](https://www.designgurus.io/learn/interview-preparation-guide)
* Leetcode for System Design: [https://bugfree.ai/practice/system-design](https://bugfree.ai/practice/system-design)
* [System Design For Beginners: Everything You Need in One Article | by Shivam Bhadani | Medium](https://medium.com/@shivambhadani_/system-design-for-beginners-everything-you-need-in-one-article-c74eb702540b#58b6) (CAP Theorem Complete)
* [System Design Fundamentals: What Every Developer Must Know](https://blogs.iamdhakrey.dev/blog/system-design-fundamentals-what-every-developer-must-know?ref=dailydev)
* [Complete Roadmap to Learn System Design for Beginners - GeeksforGeeks](https://www.geeksforgeeks.org/system-design/complete-roadmap-to-learn-system-design/)
* [System Design Roadmap](https://roadmap.sh/system-design)
* [I Gave More Than 50 Interviews in the Last 12 Months — Here Are Most Asked System Design Questions](https://javarevisited.substack.com/p/i-gave-more-than-50-interviews-in?utm_source=post-email-title\&publication_id=1444904\&post_id=173189746\&utm_campaign=email-post-title\&isFreemail=true\&r=2brj8y\&triedRedirect=true\&utm_medium=email)
* [System Design & Behavioral Interviews Practice | ](https://bugfree.ai/practice/system-design)[bugfree.ai](http://bugfree.ai)
* [✅ System Design 1: Design Tiny URL (with Advanced Design)/ URL ](https://www.youtube.com/watch?v=Y-BO_4XNw8c)
* [How to Design a URL Shortener Service (System Design Interview Guide)](https://www.designgurus.io/blog/url-shortening)
* [Proxy vs Reverse Proxy vs Load Balancer | Simply Explained](https://www.youtube.com/watch?v=xo5V9g9joFs)

\
\
\
<br>
