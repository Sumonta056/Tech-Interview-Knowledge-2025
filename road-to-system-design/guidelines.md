---
icon: lighthouse
---

# Guidelines!

{% hint style="success" %}
System design interviews at senior level aren’t asking “do you know distributed systems.” They’re asking “can I trust you to make hard calls when there’s no obvious right answer.”
{% endhint %}

#### Tricks & Rules :&#x20;

1. Need to how to approach an unclear problem under pressure?
2. Can I spend 60 seconds clarifying requirements?
3. Do you jump straight to solutions?&#x20;
4. Do you ask good questions?&#x20;
5. Do you define scope before going wide?
6. First share the basic scope, drew a rough high-level diagram, then asked the interviewer : "Which component should we focus on?"
7. **Clarify before you architect.** Ask about scale. Ask about priorities. Is this read-heavy or write-heavy? Does it need to be globally consistent or is eventual consistency fine? Two minutes of questions saves twenty minutes of going in the wrong direction.
8. Don’t assume anything silently. Say “I’m going to assume 10 million daily active users” and write it down.
9. &#x20;**Draw the boring high-level diagram first.** Not the interesting one. Not the one with all the detail.
10. **Make trade-offs visible.** Don’t just say “I’d use Kafka here.” Say “I’d use Kafka because we need guaranteed delivery and we can tolerate a few seconds of latency if we needed sub-second, I’d look at a different approach.”
11. **Identify the hard problem.** Every system design question has one genuinely hard part. For a URL shortener it’s uniqueness at scale. For a feed system it’s write fan-out. For a chat app it’s real-time delivery and ordering. Talk about that problem explicitly
12. Share thoughts. I’m trying to decide between two approaches here— let me think out loud.” Both work. Both show control.
13. You say “I’d use a relational database here.” Fine. But the interviewer is waiting for you to ask yourself: what happens when this database gets to 500 million rows? What’s the query pattern? Would a NoSQL store serve us better at that scale?
14. Say the weakness of the approach before interviewer had to point it out



#### Mistake :&#x20;

1. Going deep too fast
