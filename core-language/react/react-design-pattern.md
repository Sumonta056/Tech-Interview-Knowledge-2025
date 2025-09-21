# React Design Pattern

<details>

<summary>React Container-Presenter Pattern</summary>

Video Link: [Day 01: React Container-Presenter Pattern | Code Smell to Clean Code](https://youtu.be/1UHbhikwg-s?si=vMbx4b8GatSrBil8)

#### 📌 Pattern Idea

* **Container** → Handles logic, data fetching, state.
* **Presenter** → Pure UI, receives props, no business logic.

#### Example: `UserList`

**Container (logic)**

```jsx
// UserListContainer.jsx
import { useEffect, useState } from "react";
import UserList from "./UserList";

export default function UserListContainer() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/users")
      .then(res => res.json())
      .then(data => setUsers(data));
  }, []);

  return <UserList users={users} />;
}
```

**Presenter (UI)**

```jsx
// UserList.jsx
export default function UserList({ users }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name} – {user.email}</li>
      ))}
    </ul>
  );
}
```

**Usage**

```jsx
<UserListContainer />
```

***

✅ Container = data fetching & state\
✅ Presenter = clean, testable UI



</details>

