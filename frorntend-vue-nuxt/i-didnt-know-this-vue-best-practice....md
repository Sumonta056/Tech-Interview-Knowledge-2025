# I Didn't Know This Vue Best Practice...

#### Avoid `watchEffect` — Use `watch`

Implicit dependencies are silent bugs waiting to happen.

**❌ Before (What you might write)**

JavaScript

```
watchEffect(() => {
  // Which reactive values does this track?
  // You have no idea until runtime.
  if (user.value.isAdmin) {
    fetchPermissions(user.value.id)
  }
})
```

> Why this can hurt you: > `watchEffect` silently tracks every reactive value it touches during execution. Add one new `ref` inside and you've accidentally added a new trigger — no warning, no error. It also runs immediately, so you can't easily control when it fires.

**✅ After (What to do instead)**

JavaScript

```
watch(
  () => user.value.id,
  (newId) => {
    fetchPermissions(newId)
  },
  { immediate: true }
)
```

> Why this is safer: > `watch` is explicit about its source. You decide exactly what triggers it, when it runs first (`immediate`), and what the old vs. new values are. Zero surprise reactivity.
