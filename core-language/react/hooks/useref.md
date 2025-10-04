# useRef()

### 1. What is `useRef`?

* `useRef` is a React Hook that returns a **mutable object** with a `.current` property.
* The special part: **changing `.current` does NOT trigger a re-render**, unlike `useState`.

👉 Think of `useRef` as a “box” that React will not touch when re-rendering. The box survives between renders.

```js
const myRef = useRef(initialValue);
console.log(myRef.current); // this holds your value
```

***

### 2. Two Main Use Cases

#### (a) **Accessing DOM elements**

Sometimes you need direct access to a DOM node (like focusing an input, playing a video). `useRef` can hold a reference to that DOM element.

```jsx
import { useRef } from "react";

function InputFocus() {
  const inputRef = useRef(null);

  const handleFocus = () => {
    inputRef.current.focus(); // directly access DOM input
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={handleFocus}>Focus Input</button>
    </div>
  );
}
```

🔑 Difference: Without `useRef`, you couldn’t directly grab that input element in React.

***

#### (b) **Storing values across renders (without re-rendering)**

If you use `useState`, updating the value causes a component re-render. But with `useRef`, the value is preserved **between renders** without re-rendering.

```jsx
import { useState, useRef } from "react";

function Timer() {
  const [count, setCount] = useState(0);
  const intervalRef = useRef(null); // holds the interval id

  const startTimer = () => {
    if (intervalRef.current !== null) return; // already running

    intervalRef.current = setInterval(() => {
      setCount((c) => c + 1);
    }, 1000);
  };

  const stopTimer = () => {
    clearInterval(intervalRef.current);
    intervalRef.current = null;
  };

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={startTimer}>Start</button>
      <button onClick={stopTimer}>Stop</button>
    </div>
  );
}
```

Here:

* `intervalRef.current` holds the timer ID.
* If we stored that ID in `useState`, React would re-render unnecessarily each time.

***

### 3. Main Difference: `useState` vs `useRef`

| Feature                        | `useState`                                                       | `useRef`                                                                     |
| ------------------------------ | ---------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| Triggers re-render on change?  | ✅ Yes                                                            | ❌ No                                                                         |
| Value persists across renders? | ✅ Yes                                                            | ✅ Yes                                                                        |
| Best for...                    | UI state (something you want React to re-render when it changes) | Keeping values between renders (that don’t affect rendering) or DOM elements |

Think of it like this:

* **`useState` = data that affects the screen (UI).**
* **`useRef` = data you just want to keep around, but don’t want to show directly.**

***

### 4. Mental Model

* `useState` is like a **reactive variable**: whenever it changes, React redraws the UI.
* `useRef` is like a **secret box**: you can put stuff in it, pull it out later, but React doesn’t care about what’s inside.

{% embed url="https://youtu.be/jPMCouXondI?si=Lm4OEEIWc-FXJYGg" %}

Code : [https://github.com/tapascript/15-days-of-react-design-patterns/blob/main/day-02/controlled-uncontrolled-comp-pattern/src/controlled/ControlledFeedbackForm.jsx](https://github.com/tapascript/15-days-of-react-design-patterns/blob/main/day-02/controlled-uncontrolled-comp-pattern/src/controlled/ControlledFeedbackForm.jsx)
