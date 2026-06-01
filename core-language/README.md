---
icon: apple-core
---

# Core Language

* [Array vs LinkedList: দুটো Data Structure যেগুলো Programming-এর ভিত্তি বদলে দিয়েছে](https://www.linkedin.com/pulse/array-vs-linkedlist-%E0%A6%A6%E0%A6%9F-data-structure-%E0%A6%AF%E0%A6%97%E0%A6%B2-%E0%A6%AD%E0%A6%A4%E0%A6%A4-%E0%A6%AC%E0%A6%A6%E0%A6%B2-%E0%A6%A6%E0%A6%AF%E0%A6%9B-imran-hasan-mrvnc/)

#### Javascript

**Must Read:** [**SOLID Design Principles Every JavaScript Developer Should Know**](https://jsdev.space/solid-design-principles/?ref=dailydev)

**Must Read:** [**Real World JavaScript Interview Questions For All Skill Levels**](https://blog.webdevsimplified.com/2025-08/javascript-interview-questions/)

<details>

<summary>What is the Event Loop? How does JavaScript handle multiple lines of code, even with a single-threaded architecture?</summary>

Flow diagram: [https://theplakat.com/javascript-visualized-event-loop](https://theplakat.com/javascript-visualized-event-loop)

### 🧠 Event Loop কী?

JavaScript একটি **single-threaded** ভাষা অর্থাৎ একই সময়ে একটি কাজই করতে পারে। তাহলে প্রশ্ন হলো:

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

যখন asynchronous কাজ যেমন `setTimeout`, `fetch`, `event listener` হয় তখন সেটা call stack থেকে বের হয়ে **browser/web API** হ্যান্ডেল করে।

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

### VueJS

*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [Vue Developer Roadmap: Learn to become a Vue developer](https://roadmap.sh/vue)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [Master Vue.js Composition API: Build 10 Stunning Projects for Real-world Expertise! - YouTube](https://www.youtube.com/watch?v=pgWZLS75Nmo)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [A Vue 3 UI Framework | Element Plus](https://element-plus.org/)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [Vue Mastery | The best way to learn ](https://www.vuemastery.com/)[Vue.js](http://vue.js)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [PrimeNG](https://primeng.org/icons)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [Vue.js Crash Course - YouTube](https://www.youtube.com/watch?v=VeNfHj6MhgA)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [Build A Recipe Application With Nuxt & Tailwind CSS (Nuxt Crash Course 2025)](https://youtu.be/RAJZOqr3JZU?si=mvrz-QgDpUotTzgL)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [https://www.youtube.com/playlist?list=PL4cUxeGkcC9haQlqdCQyYmL\_27TesCGPC](https://www.youtube.com/playlist?list=PL4cUxeGkcC9haQlqdCQyYmL_27TesCGPC)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [Nuxt Auth Crash Course #13 - Prisma Integration](https://youtu.be/fSC4in0r3yU?si=nQ59x7oRPYgwJ77_)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [https://www.youtube.com/playlist?list=PL5OhSdfH4uDsunUOrTcN7Fi7Vk\_GAgSJM](https://www.youtube.com/playlist?list=PL5OhSdfH4uDsunUOrTcN7Fi7Vk_GAgSJM)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    Good One: [NuxT Server](https://youtu.be/8JJjGTLALzk?si=5s_RICWZ7v6B3GfJ)

<br>

### JavaScript

*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [denysdovhan/wtfjs: 🤪 A list of funny and tricky JavaScript examples](https://github.com/denysdovhan/wtfjs) (Undefined vs Number)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [JavaScript Developer Roadmap: Step by step guide to learn JavaScript](https://roadmap.sh/javascript)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [Problems - LeetCode](https://leetcode.com/problemset/javascript/)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [Counter - LeetCode](https://leetcode.com/problems/counter/)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [Real World JavaScript Interview Questions For All Skill Levels](https://blog.webdevsimplified.com/2025-08/javascript-interview-questions/)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [Widely used Algorithms and Data Structures using JavaScript](https://github.com/zonayedpca/AlgoDS.js)

### CSS

*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [10 CSS PRO Tips and Tricks you NEED to know](https://youtu.be/44FTAS-qT8Q?si=EI7EJlcv0rhMLLMP) : Column
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [Master CSS ::before and ::after in 3 Minutes!](https://www.youtube.com/watch?v=g8by6hIpJPA)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [Flexbox Labs](https://flexboxlabs.netlify.app/)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [5 CSS Tips & Tricks for better Responsive Web Design](https://youtu.be/2IV08sP9m3U?si=UrXuuYYTvoVrVK_y)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [CSS Grid Course - The Only Grid Tutorial You'll Ever Need!](https://www.youtube.com/watch?v=JYfiaSKeYhE)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [CSS Grid Generator (Drag & Drop)](https://cssgridgenerator.io/)

<br>

### Git Basic

*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [分支管理 Branching (76m)\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1Xy4y1n7D1?spm_id_from=333.788.videopod.episodes\&vd_source=0a30c657e0cdfb79355208a4ffb0b54f\&p=4)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [The Ultimate Git Course](https://codewithmosh.com/p/the-ultimate-git-course)

\
<br>

### Backend Topics

*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    Caching with Redis
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [How to Implement Query Caching with Redis, Express, Node.js, and React | by Grant Ferowich | Medium](https://medium.com/@gferowich/how-to-implement-query-caching-with-redis-express-node-js-and-react-3a4c4b530c6)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [Caching with Redis and Express Middleware](https://redis.io/learn/develop/node/nodecrashcourse/caching)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [How to Setup Redis Caching in Node - YouTube](https://www.youtube.com/watch?v=QqTB97aMa4c)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    Rate Limiting
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [REST API Rate Limiter in NodeJS using Redis and ExpressJs | REST API Rate Limiter | Redis](https://www.youtube.com/watch?v=MChPvKTTIMk)

\
<br>

### React JS

*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [React Foundations: Rendering User Interfaces (UI) | ](https://nextjs.org/learn/react-foundations/rendering-ui)[Next.js](http://next.js) \[Basic]
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [Designing State in React Interviews](https://www.greatfrontend.com/react-interview-playbook/react-state-design) \[Important]
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [Learn React Escape Hatches – React JS Bangla Tutorial](https://react-bangla.vercel.app/learn-react-escape-hatches)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [codedbyMojnu/react-bangla-tutorial: React Bangla Tutorial - স্টেপ বাই স্টেপ ফলো করে রিয়েক্ট শিখুন।](https://github.com/codedbyMojnu/react-bangla-tutorial)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [Day 06: MASTER React Custom Hook Pattern with Real-World Examples](https://www.youtube.com/watch?v=fhwvqTry_Vg)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [How to make React faster! - Learning Dev Core Topics - GitBook](react/how-to-make-react-faster.md)
*

    ![unchecked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAAA1ElEQVR4Ae3bMQ4BURSFYY2xBuwQ7BIkTGxFRj9Oo9RdkXn5TvL3L19u+2ZmZmZmZhVbpH26pFcaJ9IrndMudb/CWadHGiden1bll9MIzqd79SUd0thY20qga4NA50qgoUGgoRJo/NL/V/N+QIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIECBAgQIAAAQIEyFeEZyXQpUGgUyXQrkGgTSVQl/qGcG5pnkq3Sn0jOMv0k3Vpm05pmNjfsGPalFyOmZmZmdkbSS9cKbtzhxMAAAAASUVORK5CYII=)

    [sudheerj/reactjs-interview-questions: List of top 500 ReactJS Interview Questions & Answers....Coding exercise questions are coming soon!!](https://github.com/sudheerj/reactjs-interview-questions)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [Day 01: React Container-Presenter Pattern | Code Smell to Clean Code](https://www.youtube.com/watch?v=1UHbhikwg-s)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [Day 02: React Design Patterns || Controlled & Uncontrolled Forms - YouTube](https://www.youtube.com/watch?v=jPMCouXondI\&t=1961s)
*

    ![checked](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAYAAABV7bNHAAABbElEQVR4Ae3bsU4CYRDEcRsxodZE8Q0BbS258l5MwESJNL6HOfrPKdhyxeBcwk5mkn9F98sGIOSuPM/zPM/zPI+xG/SEtuiAWpEOaIOWaDIWziP6RK14OzSjX44ITvTBvqRn1MRaMIHeBIE2TKBBEGhgArWkKmtJBjKQgQxkIANd/Aw0NVC+O7RHvYFynHasN1COE/UGynGiXgOIjxOtdIH4OGJAfBwxID6OGBAfRwiIjyMARMCpCjRF5+72Dzhd5R+rHfpC92NeTlWgLl5PkQg4RYBynBSJgFMGKMNJkQg4lYFeUDuFRMCpBXQOEgGnDtA/kPg4xT7m2y/tCd9zKgOdviTC5RQEIiAFjh4QASlw9IAISIEjCURAWvmf1UDKcQwUSDmOgWLdMcxA7BnIQAYykIEM5EcRvplAW0GgNRNoKQg0ZwJN0E4I5x1dI+pmgSSA84BG2QQt0LrYG/eAXtGccjme53me53me9wPjPWZWjhktAQAAAABJRU5ErkJggg==)

    [How To Handle Data Access Like a Senior Dev\
    <br>](https://www.youtube.com/watch?v=Av7KqJVLiYc)

Currently Watching :

***

* [Build Complete REST API in NestJs #1 - with TypeScript & MongoDB](https://youtu.be/4oLUddZVL80?si=BstlKK6U4Ar-N5cH)
* [Deploy NodeJS Application on AWS - Amazon Web Services | NodeJS - YouTube](https://www.youtube.com/watch?v=_U0pTlpyMGg)
* ​​[Elasticsearch Roadmap](https://roadmap.sh/elasticsearch) \*\*\*
* <br>

<br>

Resources

***

* [Introduction to Arrays](https://www.designgurus.io/course-play/grokking-data-structures-for-coding-interviews/doc/introduction-to-arrays)
* visualizing[ data structures and algorithms through animation - VisuAlgo](https://visualgo.net/en)

<br>

Solve List

***

* <br>

\
\
\
\
<br>

#### NextJS

<details>

<summary>Why Only the First Page is Truly Server-Side Render in Next.js? Rest of the page actually render in client side.</summary>

একটা অবাক করা ব্যাপার হলো — Next.js-এ আপনি যতই পেজ server-side render করুন না কেন, আপনার হিট করা শুধুমাত্র প্রথম পেজটাই server-side render হয়। বাকি সব পেজ ঠিকই client-side এ render হয়!

অনেকেই মনে করেন, “আমি তো সব পেজেই getServerSideProps দিয়েছি, তাহলে তো সব পেজই SSR হওয়া উচিত!”\
কিন্তু বাস্তবতা হলো — আপনি যে পেজে প্রথমবার ঢুকছেন, শুধু সেই পেজের HTML server থেকে পাঠানো হয়। বাকি পেজগুলোতে আপনি দিয়ে গেলে, Next.js backend থেকে .rsc বা .json ফর্মে data এনে client-side এ render করে।

বর্তমানে অনেক ডেভেলপার Next.js দিয়ে কাজ করলেও এর ভিতরের rendering behavior ঠিকভাবে বোঝে না — বিশেষ করে যেটা আমি বুঝেছি: প্রথম পেজে server থেকে HTML আসে, কিন্তু বাকি পেজে গেলে শুধু .rsc বা virtual DOM এর মতো ডেটা আসে — HTML না। কেন?

আমিও শুরুতে মনে করতাম, সব পেজেই যদি server থেকে HTML পাঠানো হতো, তাহলে SEO আরও ভালো হতো। কিন্তু, বাস্তবতা অনেক গভীর এবং চমকপ্রদ!

চলুন ব্যাখ্যা করি:\
Next.js মূলত একটি Hybrid Rendering Framework — এটি intelligent ভাবে বেছে নেয় কখন SSR (Server-Side Rendering), কখন SSG (Static Site Generation), আর কখন CSR (Client-Side Rendering) হবে।

যখন একজন user প্রথমবার কোনো পেজে ঢুকে, Next.js সেই পেজের HTML server থেকে পাঠায় — এতে SEO ও performance দুটোই বজায় থাকে।

কিন্তু এরপর যদি user ব্যবহার করে অন্য পেজে যায়, Next.js তখন আর পুরো HTML পাঠায় না। পাঠায় .rsc, .json, বা component-level data — যেটা browser নিজে render করে client-side এ। এটা হয়:\
SPA feel দেওয়ার জন্য (reload ছাড়া smooth navigation)\
Performance boost করার জন্য (HTML heavy নয়, শুধু প্রয়োজনীয় data)\
এবং Server load কমানোর জন্য

তাহলে প্রশ্ন: এটা কি SEO-তে ক্ষতি করে?\
না। Google বা অন্যান্য SEO bot সাধারণত প্রথমবার যেই পেজে ঢুকছে, শুধু সেটাকেই crawl করে। তারা JavaScript execution করে না, internal navigation follow করে না।\
তাই যদি প্রতিটি SEO-critical পেজ (যেমন homepage, blog, product details) SSR বা SSG করা থাকে, তাহলে SEO damage হয় না — বরং performance বাড়ে।

এই Smart Rendering System এর কারণেই Next.js এখনকার দিনে সবচেয়ে শক্তিশালী React Framework গুলোর একটি।\
তবে, যদি কারও প্রজেক্টে এমন প্রয়োজন হয় যে, প্রত্যেকটি পেজেই server-rendered HTML লাগবে (যেমন গ্লোবাল নিউজ পোর্টাল বা government portal) — তখন Next.js সেই ফ্লেক্সিবিলিটিও দেয়:\
getServerSideProps বা App Router-এ dynamic = "force-dynamic" দিয়ে।

একজন ডেভেলপার হিসেবে আমার অভিজ্ঞতা বলছে:\
Next.js শিখতে শুধু component বানানোই যথেষ্ট নয়, বরং এর architecture, rendering pipeline, এবং server-client responsibility deeply বোঝা খুব দরকার — বিশেষ করে যারা Performance, SEO, এবং Scalability-র দিকে ফোকাস করতে চায়।

Author : [Tanver Ahmed](https://www.linkedin.com/in/tanver-js/overlay/about-this-profile/)

</details>
