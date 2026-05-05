---
icon: traffic-light
---

# Incident Checklist

### First 2 minutes (don't touch anything yet)

* [ ] When did it start? Check logs for the exact timestamp
* [ ] What changed recently? (deploy in last 24h? config change? scheduled job?)
* [ ] Is it affecting ALL users or specific ones?
* [ ] Is it getting worse, stable, or recovering on its own?

### Communicate first

* [ ] Tell your team lead / senior dev immediately — don't investigate alone in silence
* [x] Write a one-liner: "API returning 500s since 14:32, affecting /checkout, investigating"
* [x] Never say "I don't know what happened" — say "I'm investigating, update in 10 min"

### Narrow it down

* [ ] Is the database responding? (check connection, run a simple query)
* [ ] Is it a specific endpoint or everything?
* [ ] Did it start at a specific time that matches something? (deploy, traffic spike, cron job)
* [ ] Are the logs showing an error message or just timeouts?

### Before you "fix" anything

* [ ] Do you understand WHY it's broken, or are you guessing?
* [ ] Is your fix reversible? (if not, be very careful)
* [ ] Have you told someone what you're about to do?
