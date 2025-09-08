# JS/TS

<p align="center"><strong>Must Read:</strong> <a href="https://jsdev.space/solid-design-principles/?ref=dailydev"><strong>SOLID Design Principles Every JavaScript Developer Should Know</strong></a></p>

<details>

<summary>What is the Event Loop? How does JavaScript handle multiple lines of code, even with a single-threaded architecture?</summary>

Flow diagram: [https://theplakat.com/javascript-visualized-event-loop](https://theplakat.com/javascript-visualized-event-loop)

### 🧠 Event Loop কী?

JavaScript একটি **single-threaded** ভাষা  অর্থাৎ একই সময়ে একটি কাজই করতে পারে। তাহলে প্রশ্ন হলো:

> 👉 **"যখন কেউ API call দেয়, বা বড় কোনো file পড়ে, তখন তো সেই কাজ শেষ না হওয়া পর্যন্ত পুরো কোড আটকে যাওয়ার কথা!"**

এখানেই আসে **Event Loop**। এটা এমন একটা মেকানিজম, যেটা JavaScript কে **non-blocking**, **asynchronous**, এবং **efficient** ভাবে কাজ করতে সাহায্য করে।

***

### 🎯 Core Concepts বোঝা যাক:

#### 1. **Call Stack**:

এখানে সব synchronous (তাত্ক্ষণিক) কাজ একের পর এক যায়। যেমন: `console.log`, math calculation ইত্যাদি।

```js
console.log('Hello');
// এটি সরাসরি Call Stack এ ঢুকে execute হয়।
```

#### 2. **Web APIs (Browser APIs)**:

যখন asynchronous কাজ যেমন `setTimeout`, `fetch`, `event listener` হয়  তখন সেটা call stack থেকে বের হয়ে **browser/web API** হ্যান্ডেল করে।

```js
setTimeout(() => {
  console.log('Timer');
}, 1000);
```

> এখানে browser নিজে টাইমার চালায়, JS ইঞ্জিন না।

#### 3. **Callback Queue (or Task Queue)**:

যখন Web API তার কাজ শেষ করে, তখন result বা callback function এই queue তে পাঠিয়ে দেয়।

#### 4. **Event Loop**:

এটাই পুরো orchestra-র কন্ডাক্টর। Event loop একটার পর একটা করে চেক করে **“Call Stack ফাঁকা হয়েছে কিনা?”** যদি ফাঁকা থাকে, তাহলে **Callback Queue** থেকে একটা কাজ টেনে নিয়ে Call Stack এ ঢুকিয়ে দেয়।

***

### 🔁 বাস্তব উদাহরণ আবার দেখা যাক:

```js
console.log('Start');

setTimeout(() => {
  console.log('Inside Timeout');
}, 0);

console.log('End');
```

#### ➤ Execution Flow:

1. `'Start'` → Call Stack → Console এ print ✅
2. `setTimeout()` → Web API-তে চলে যায় 🕐
3. `'End'` → Call Stack → Console এ print ✅
4. Timer শেষ হলে callback যায় Callback Queue তে ⏳
5. Event Loop দেখে Call Stack ফাঁকা → Callback Queue থেকে callback নিয়ে Call Stack এ পাঠায়
6. `'Inside Timeout'` → Console এ print ✅

> ⚠️ যদিও `setTimeout` এর delay `0ms`, কিন্তু তাও সেটা আসতে কিছু সময় লাগে। তাই সেটা **সবশেষে** execute হয়।

***

### 🧵 কেন JavaScript একে একে কাজ করে?

কারণ JavaScript Engine (যেমন V8) শুধুমাত্র **একটা Main Thread** ব্যবহার করে। একারণেই **multi-tasking** করার জন্য এই Event Loop দরকার হয়।

### 🧪 আরও কিছু Terms জানলে ভালো বোঝা যাবে:

<table><thead><tr><th width="215">Term</th><th>কাজ</th></tr></thead><tbody><tr><td><strong>Microtask Queue</strong></td><td><code>Promise</code>, <code>MutationObserver</code> ইত্যাদির জন্য আলাদা queue</td></tr><tr><td><strong>Macrotask Queue</strong></td><td><code>setTimeout</code>, <code>setInterval</code>, <code>setImmediate</code> ইত্যাদির জন্য</td></tr><tr><td><strong>Job Queue Priority</strong></td><td>Microtasks always execute <strong>before</strong> macrotasks</td></tr></tbody></table>

```js
console.log("1");

setTimeout(() => {
  console.log("2");
}, 0);

Promise.resolve().then(() => {
  console.log("3");
});

console.log("4");
```

👉 Output হবে:

```
1
4
3
2
```

> কারণ `Promise.then()` যায় **microtask queue** তে, আর সেটা **priority higher than setTimeout** (macrotask)।

***

### ✅ সংক্ষেপে মনে রাখো:

🔸 **Call Stack** — সরাসরি কাজ এখানে চলে\
🔸 **Web APIs** — async কাজ এখানে হয়ে callback তৈরি করে\
🔸 **Callback Queue** — কাজ প্রস্তুত হয়ে বসে থাকে\
🔸 **Event Loop** — দেখে call stack ফাঁকা হলে কাজ সরিয়ে আনে\
🔸 **Microtasks আগে, তারপর Macrotasks**

</details>

<details>

<summary>What’s the difference between Type and Interface in TypeScript?</summary>

So What’s the Real Difference?

**1. Extensibility: The Big One**

`interface` can be **reopened**. Like a restaurant that closes at 3 PM and magically reopens at 7 with a new menu.

```ts
interface Cat {
  meow: () => string;
}

// Later, somewhere else in your code...
interface Cat {
  purr: () => string;
}

// Boom. Cat now has both meow AND purr.
// TypeScript just… merged them. No drama.
```

Try that with `type`? Nope. Compiler throws a fit. _“Cannot redeclare ‘Cat’”_. It’s a one-shot deal. Like a tattoo you regret at 2 AM.

```ts
type Dog = {
  bark: () => string;
};

type Dog = {
  wagTail: () => void;
}; // ❌ Error. TypeScript says: "Nah, bro. Pick one."
```

So, if you’re building a library, or expect your types to evolve across files? `interface` is your BFF.

***

**2. Flexibility in Shape**

`type` doesn’t play by the same rules. It’s… wilder. Can represent **unions**, **tuples**, **mapped types**, and **conditional types** — stuff `interface` just can’t handle.

```ts
type Status = 'loading' | 'success' | 'error';
type Coordinates = [number, number];
type Maybe<T> = T | null | undefined;
```

Try doing that with `interface`? Good luck. You’ll end up with 17 interfaces and a therapist.

`interface` is strict. It likes objects. It likes structure. It drinks black coffee and reads the spec before bed.

`type`? It’s the one at the party doing handstands on the couch, yelling, _“I can be a string OR a function OR a recursive tree — deal with it!”_

***

**3. Merging vs. Intersection**

`interface` merges automatically. Like two rivers joining.

```ts
interface User {
  id: number;
}

interface User {
  name: string;
}

// User now has id + name. Magic? Or just TypeScript being slick?
```

`type`? No merging. But you can **intersect**:

```ts
type Id = { id: number };
type Name = { name: string };
type User = Id & Name; // Same result, but manual work.
```

It’s like building a sandwich. `interface` hands you a fully stacked one. `type` gives you ingredients and a knife. You do the slicing.

***

**4. Performance & Tooling**

Here’s a spicy take: **interfaces are slightly better for large-scale projects**. Why? Because TS can optimize them. Faster autocomplete. Smoother refactoring. Less “TS Server is thinking…” moments.

Types? They’re heavier. Especially complex unions. Can slow down IDEs. Not a dealbreaker. But if you’re working on a codebase the size of a small moon? Every millisecond counts.

***

#### So… What Should You Use?

Let’s get real. There’s no _one_ answer. But here’s my rule of thumb — forged in fire, broken builds, and late-night debugging:

> **Use `interface` for public APIs, objects, and things that might grow.**\
> **Use `type` when you need flexibility — unions, tuples, or complex logic.**

Examples?

✅ **Go for `interface`:**

* Shapes of objects (users, config, API responses)
* Classes implementing contracts
* Libraries or shared code
* Anything you might extend later

✅ **Go for `type`:**

* Union types (`'dark' | 'light'`)
* Tuples (`[string, number]`)
* Function signatures with overloads
* Conditional or mapped types
* When you need `&` or `|` in the definition

And hey — don’t overthink it. If you’re just starting? Pick `interface` for objects. It’s safer. More predictable. Like wearing socks with sandals — functional, even if not trendy.

***

#### A Few Curveballs

You _can_ extend an `interface` from a `type` — but only if the type is object-like.

```ts
type Animal = { sound: string };
interface Dog extends Animal { breed: string; } // ✅ Works
```

But not the other way around if the type uses unions or primitives.

And `type` can mimic `interface` using `&`, but it’s clunkier. Like using duct tape to fix a Rolex.

***

#### Final Thoughts?

It’s not about which is _better_. It’s about **fit**.

Think of `interface` as a well-tailored suit — clean, structured, meant to be built upon.\
`type`? That’s your Swiss Army knife. Not pretty, but damn useful when things get weird.

Source: [https://app.daily.dev/posts/what-s-the-difference-between-type-and-interface-in-typescript--k7yr5ybi5](https://app.daily.dev/posts/what-s-the-difference-between-type-and-interface-in-typescript--k7yr5ybi5)

</details>









