# useMemo()

{% embed url="https://youtu.be/THL1OPn72vo?si=KW3gF5c9FVGTgbdS" %}



* **What**
  * A React hook to **cache (memoize) values** so they don’t get recalculated on every render.
* **Why**
  * Prevents **slow/expensive functions** from re-running unnecessarily.
  * Keeps **object/array references stable** to avoid unwanted re-renders or `useEffect` triggers.

***

* **How (Slow Function Example)**

```jsx
const doubledNumber = useMemo(() => {
  return slowDouble(number); // heavy function
}, [number]);
```

Runs `slowDouble` only when `number` changes, not on every render.

***

* **How (Referential Equality Example)**

```jsx
const themeStyles = useMemo(() => {
  return { background: dark ? 'black': 'white' };
}, [dark]);
```

Object reference stays the same unless `dark` changes, so `useEffect` won’t fire unnecessarily.

***

* **When&#x20;**_**Not**_**&#x20;to Use**
  * Don’t wrap **cheap/simple calculations** (adds overhead).
  * Don’t use it “everywhere” — only when:
    1. Function is **slow/heavy**.
    2. You need **stable references** for objects/arrays.

***

Think of `useMemo` , like **“lazy caching”**:

* Use it when things are **expensive** or **sensitive to references**.
* Skip it if your code is already fast & simple.

***

### Overhead of `useMemo`&#x20;

* React must:
  * **Store the previous value/function** in memory.
  * **Compare dependencies** on every render.
  * **Call an extra function** (`useMemo` ) every render.
* These overheads are **tiny**, but if you wrap _everything_, it can:
  * Increase **memory usage** (caching values/functions that don’t need caching).
  * Add **extra checks** on every render → ironically **slower** than just recalculating small things.









