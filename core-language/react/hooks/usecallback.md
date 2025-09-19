# useCallback()

{% embed url="https://youtu.be/_AyFP5s69N4?si=iOeYdhRM2SGgeFfC" %}

### Useful Resources:

* [https://www.telerik.com/blogs/react-basics-how-use-react-usecallback-effectively?ref=dailydev](https://www.telerik.com/blogs/react-basics-how-use-react-usecallback-effectively?ref=dailydev)

***

* **What**
  * A React hook that **caches a function** so it doesn’t get recreated on every render.
  * Similar to `useMemo`, but instead of caching a **value**, it caches a **function reference**.
* **Why**
  * Prevents unnecessary re-runs of `useEffect` or re-renders of child components caused by **new function references** in each render.
  * Fixes **referential equality problems** (a new function is technically a new object, even if the logic is the same).

***

* **How (Basic Example)**

```jsx
const getItems = useCallback(() => {
  return [number, number + 1, number + 2];
}, [number]);

<List getItems={getItems} />
```

👉 `getItems` is recreated **only when `number` changes**, not when unrelated state (like theme) changes.

***

* **How (With Parameters)**

```jsx
const getItems = useCallback((increment) => {
  return [number + increment, number + 1 + increment, number + 2 + increment];
}, [number]);

getItems(5); // returns [num+5, num+6, num+7]
```

👉 Unlike `useMemo`, `useCallback` still gives you back a **function** you can call later with arguments.

***

* **When&#x20;**_**Not**_**&#x20;to Use**
  * Don’t use it just for _every_ function — creating small functions is cheap.
  * Use it mainly when:
    1. Passing functions as **props to memoized children**.
    2. Functions appear in a **dependency array** (`useEffect`, `useMemo`).
  * Rare case: if creating the function itself is _really slow_ (almost never happens).

***

### &#x20;`useMemo` vs `useCallback`

<table><thead><tr><th width="127">Hook</th><th>What it returns</th><th>Use case</th><th>Example</th></tr></thead><tbody><tr><td><strong><code>useMemo</code></strong></td><td><strong>Value</strong> (the result of a function)</td><td>Cache a <strong>calculated value</strong> so it doesn’t recompute every render</td><td><code>js const doubled = useMemo(() => slowDouble(num), [num]);</code> → <code>doubled</code> is a <strong>number</strong></td></tr><tr><td><strong><code>useCallback</code></strong></td><td><strong>Function</strong> (the function itself)</td><td>Cache a <strong>function reference</strong> so it doesn’t get recreated every render</td><td><code>js const getItems = useCallback(() => [num, num+1], [num]);</code> → <code>getItems</code> is a <strong>function</strong></td></tr></tbody></table>

***

#### ✅ Think of it like this:

* **`useMemo`** → “Give me the **answer** now, but cache it.”
* **`useCallback`** → “Give me the **formula** (function) itself, but cache it.”

***

#### 🧑‍💻 Tiny Real Example

```jsx
// ❌ Without memo/callback
const doubled = slowDouble(num);       // recalculates every render
const getItems = () => [num, num + 1]; // new function every render

// ✅ With useMemo
const doubled = useMemo(() => slowDouble(num), [num]); 
// only recalculates when num changes

// ✅ With useCallback
const getItems = useCallback(() => [num, num + 1], [num]);
// same function reference unless num changes
```

***

👉 In short:

* Use **`useMemo`** when you need to **store a computed value**.
* Use **`useCallback`** when you need to **store a function**.









