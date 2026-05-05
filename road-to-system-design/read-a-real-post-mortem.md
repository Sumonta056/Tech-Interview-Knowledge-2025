---
icon: inbox-in
---

# Read a real post-mortem

This is the highest-leverage thing you can do this week. Real companies publish incident reports — and they're more educational than any tutorial.

Read one of these (all free, all beginner-readable):

* **Cloudflare:** cloudflare.com/blog — search "incident report" or "post-mortem". Their June 2019 BGP leak is legendary and well-written.
* **GitHub:** githubstatus.com : click any past incident to see their timeline.
* **Fly.io:** community.fly.io : they write unusually honest post-mortems.

While reading, answer these questions in a notebook or doc:

```
1. What was the user-visible symptom? (what did users see?)
2. What was the actual root cause? (often different from the symptom)
3. How long until they detected it?
4. How long until they fixed it?
5. What made it worse? (often: the first "fix" attempt)
6. What did they change afterward?
```

Notice the pattern: the symptom (slow API) is almost never the root cause (someone deployed a bad config, a database index got dropped, a third-party service went down). Junior devs fix symptoms. Senior devs find root causes.
