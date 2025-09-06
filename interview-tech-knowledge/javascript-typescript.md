# Javascript - TypeScript

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
