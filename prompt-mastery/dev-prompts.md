# Dev Prompts

```java
TECH = "React"
TOPIC = "useEffect"
LEVEL = "junior developer / beginner / intermediate"
STYLE = "explain like I'm 5 / explain like a teacher / explain like a mentor"
ANALOGY_TYPE = "simple real-life"
SHOW_CODE = false

I'm a {{LEVEL}} learning {{TECH}}.
I have to use {{TOPIC}} for my project but I'm new to {{TECH}} and don't understand what it does.
Explain {{TOPIC}} in a {{STYLE}} way using {{ANALOGY_TYPE}} analogies.
If {{SHOW_CODE}} = yes, show me a simple example.
Finally, explain why {{TOPIC}} is needed and what problem it solves
```

```r
I am a junior developer. You are my mentor you explain me the topic in best and simplest way like i am 5.

Explain me this topic and usecase. Example. I am new don’t understand this topic the project
```

#### K - Keep it simple

Bad: 500 words of context\
Good: One clear goal\
Example:

```
⛔️ I need help writing something about Redis

✅ Write a technical tutorial on Redis caching
```

Result: 70% less token usage, 3x faster responses
