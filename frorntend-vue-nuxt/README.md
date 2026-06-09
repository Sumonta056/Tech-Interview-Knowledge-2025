---
icon: react
---

# Frontend: Vue - Nuxt

<details>

<summary>Difference between ≠ vs !==?</summary>

### 🧩 1️⃣ `!=` vs `!==` (JavaScript — not Vue specific)

These are **comparison operators**, but they behave _slightly differently_.

| Operator | Meaning            | Type Check | Example     | Result  |
| -------- | ------------------ | ---------- | ----------- | ------- |
| `==`     | Equal to (loose)   | ❌ No       | `"5" == 5`  | ✅ true  |
| `!=`     | Not equal (loose)  | ❌ No       | `"5" != 5`  | ❌ false |
| `===`    | Equal to (strict)  | ✅ Yes      | `"5" === 5` | ❌ false |
| `!==`    | Not equal (strict) | ✅ Yes      | `"5" !== 5` | ✅ true  |

💡 **Rule of thumb:**\
👉 Always use the **strict** ones (`===` and `!==`) — they avoid weird bugs.

**Example in Vue:**

```vue
<div v-if="userAge !== 18">
  You are not 18.
</div>
```

✅ This checks both **value** and **type** strictly.

</details>

<details>

<summary>Why <code>submit.prevent?</code>Why we use <code>prevent.default</code>?</summary>

👇 Example:

```vue
<form @submit.prevent="handleSubmit">
  <button type="submit">Submit</button>
</form>
```

Normally, when a form submits, the **browser reloads the page** (the default HTML behavior).\
But in Vue (and most modern web apps), we don’t want that — we handle submission ourselves (via JS).

💡 So:

* `.prevent` means “prevent the default browser action.”

👉 Think of it as:

> “Don’t refresh the page, I’ll handle it with Vue instead.”

</details>

<details>

<summary>Why use <code>click.stop?</code> What is the use case of <code>stop</code> ?</summary>

👇 Example:

```vue
<div @click="outerClick">
  <button @click.stop="innerClick">Click Me</button>
</div>
```

Without `.stop`:\
When you click the button → Vue runs both `innerClick()` **and** `outerClick()`\
(Because the click “bubbles up” to the parent div.)

With `.stop`:\
The event stops there — only `innerClick()` runs.

💡 So:

* `.stop` means “stop the event from bubbling up to parent elements.”

👉 Think of it as:

> “Only this element should react to the click — not its parents.”

</details>

<details>

<summary>Dynamic Slots? <code>#dynamicSlot</code></summary>

Slots let you pass **custom content** from parent to child.\
A _dynamic slot_ means the **slot name itself** is dynamic (changes at runtime).

***

#### 🎯 Example:

**🧱 Child.vue**

```vue
<template>
  <div>
    <slot name="header">Default Header</slot>
    <slot name="footer">Default Footer</slot>
  </div>
</template>
```

**🧩 Parent.vue**

```vue
<script setup>
import Child from "./Child.vue";
const slotName = "header"; // could change dynamically
</script>

<template>
  <Child>
    <template v-slot:[slotName]>
      <h1>This goes into the header slot</h1>
    </template>
  </Child>
</template>
```

💡 Here, `v-slot:[slotName]` means:

> “Bind to the slot whose name is in the variable `slotName`.”

So if `slotName = 'header'`,\
→ the content goes into `<slot name="header">`.

If later `slotName = 'footer'`,\
→ it’ll go into `<slot name="footer">` dynamically.

</details>

#### 𝗝𝗮𝘃𝗮𝗦𝗰𝗿𝗶𝗽𝘁 (𝗖𝗼𝗿𝗲 𝗗𝗲𝗽𝘁𝗵)

1\. Explain the JavaScript event loop. Microtasks vs macrotasks?

2\. Implement Promise.all from scratch.

3\. Implement debounce and throttle.

4\. Explain closures with a real-world example.

5\. How does prototypal inheritance work?

6\. Difference between var, let, and const.

7\. Difference between == and ===.

8\. Explain shallow copy vs deep copy. Implement deep clone.

9\. How does this behave in arrow functions, class methods, and event handlers?

10\. Explain call, apply, and bind with use cases.

***

#### 𝗗𝗦𝗔 (𝗙𝗿𝗼𝗻𝘁𝗲𝗻𝗱 𝗥𝗼𝘂𝗻𝗱)

31\. Maximum sum subarray (Kadane’s algorithm).

32\. Sliding window maximum.

33\. Merge k sorted linked lists.

34\. Detect a cycle in a linked list.

35\. Longest palindromic substring.

36\. Design and implement an LRU Cache.

***

#### Resource :

* [DEBUGGING HACK: How I Fixed a Production Bug (No Backend Access!) 🪲](https://www.youtube.com/watch?v=ndrPcDNmwFk)
* [https://www.curotec.com/interview-questions/125-vue-js-interview-questions/#advanced-level](https://www.curotec.com/interview-questions/125-vue-js-interview-questions/#advanced-level)
