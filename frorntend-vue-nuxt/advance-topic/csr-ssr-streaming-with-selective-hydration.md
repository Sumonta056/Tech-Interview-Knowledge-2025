# CSR, SSR, Streaming with Selective Hydration

## 🟢 First: What does **Hydration** mean?

When you get HTML from SSR or Streaming:

* The **HTML already looks correct** (you see text, images, etc.).
* But it’s just **static HTML** → buttons, dropdowns, forms don’t work.
* React (or any framework) must **attach JavaScript event listeners** (click, hover, submit) to the DOM.

👉 This process is called **hydration** = “taking static HTML and making it alive/interactive with JS.”

**Mini Story:** Think of SSR HTML as a **toy robot in a shop window**. It looks real, but doesn’t move. Hydration is when you put **batteries inside** → now the robot walks and talks

***

### 1. **CSR (Client-Side Rendering)**

> 👉 Render everything **on the browser** after downloading a JavaScript bundle.

#### How it works

1. Browser loads an empty/**blank HTML** (`<div id="root"></div>`)
2. Downloads JavaScript bundle (React Code `main.js`)&#x20;
3. JS (React) executes → builds the DOM for product title, price, and button.
4. User sees the page after everything loads.

🔴 User waits → bad for SEO.

#### Pros

* Rich interactivity.
* Easy to scale since the server just serves static files (CDN-friendly).
* Good for apps that are heavily **stateful** (dashboards, Gmail, Figma).

#### Cons

* **Slow First Paint** (white screen until JS arrives and runs).
* SEO suffers (search engine crawlers might not wait for hydration).

#### Story Example 🏪

Imagine visiting a **restaurant** where:

* You walk in → all tables are empty, no menus.
* Waiter says: “Hold on, I’ll bring everything.”
* After some time, the waiter brings the table, chairs, food, and cutlery **all at once**.
* That’s CSR → **blank page until JavaScript sets up everything.**

***

### 2. **SSR (Server-Side Rendering)**

> 👉 Render HTML on the server, send a fully built page → browser just displays.

#### How it works

1. The browser requests the product page.
2. Server runs React → generates HTML string with product title, price, “Buy Now” button.
3. Sends ready HTML to the browser.
4. The browser immediately shows product info (static).
5. Later, React JS hydrates → button becomes clickable.

🟢 Fast to see content, but hydration still takes time.

#### Pros

* **Fast First Paint** → better UX & SEO.
* Good for content-heavy pages (blogs, e-commerce, landing pages).

#### Cons

* Server CPU cost (rendering HTML repeatedly).
* Slower for user interactions (extra hydration step).

#### Story Example 🍔

Imagine visiting **McDonald’s**:

* You order a burger.
* The kitchen prepares it **before you sit down**.
* You instantly get a **ready-to-eat burger**.
* That’s SSR → server does the work, you just eat.

***

### 3. **Streaming (SSR with Streaming)**

> 👉 Server doesn’t wait to generate the **entire HTML**. It sends HTML **in chunks** as soon as ready.

#### How it works

1. The browser requests the product page.
2. The server first sends skeleton: Header + placeholder for product.
3. The browser shows the **header instantly**.
4. Server fetches price, reviews, and streams them one by one → browser updates.
5. The browser shows content progressively.

🟢 Feels fast, even when data is slow.

In Summary :&#x20;

* Problem it solves: **Slow server responses** (e.g., some data takes longer to fetch).
* Approach: Send HTML **piece by piece** instead of waiting for all.
* Benefit: User **sees content earlier** (progressive render).

#### Pros

* Ultra-fast initial render (users see skeleton instantly).
* Better perceived performance.
* Works well with **slow APIs** or big pages (news feeds, search results).

#### Cons

* Harder to implement/debug.
* Needs modern infra (HTTP/2, React 18+ streaming).

#### Story Example 📺

Imagine Netflix:

* You open a movie page.
* First, you see the **movie title + poster**.
* Then, **synopsis loads**, then **reviews**, then **similar movies**.
* That’s Streaming → server streams chunks instead of waiting for all.
* All is still static HTML until hydration.

***

### 4. **Selective Hydration** (React 18+ feature)

> 👉 Instead of hydrating the **entire HTML at once**, only hydrate interactive parts **on demand**.

#### How it works

* Server sends fully rendered HTML (SSR or streaming).
* The browser shows a static page instantly.
* React hydrates **only important widgets first** (e.g., navbar, search box).
* Other parts (like the footer, carousels) hydrate **later**.

In Summary :&#x20;

* Problem it solves: **Slow interactivity** when the page has many components.
* Approach: Hydrate **important parts first** (e.g., “Play” button) → make interactive earlier, while other parts hydrate later.
* Benefit: The User can **interact earlier**, even if the page is huge.

#### Pros

* Faster interaction for critical UI (search, login button).
* Saves CPU & battery (especially on mobile).
* Great for big apps with many components.

#### Cons

* Needs careful planning, which components hydrate first?
* Not supported by all frameworks yet.

#### Story Example 🎢

Think of **Disneyland**:

* Park opens at 9 AM.
* Not all rides open instantly.
* **Main rides (roller coasters, Ferris wheel)** open first → people start enjoying.
* Later, smaller rides (tea cups, mini-games) open gradually.\
  That’s Selective Hydration → **critical UI works first, rest catches up later**.

***

| Technique               | Best Use Case                                             | Example App                            |
| ----------------------- | --------------------------------------------------------- | -------------------------------------- |
| **CSR**                 | Heavy app logic, less SEO needed                          | Figma, Gmail, Notion                   |
| **SSR**                 | SEO + fast initial page load                              | E-commerce, Blogs                      |
| **Streaming SSR**       | Large content, progressive loading                        | Netflix, Twitter Feed                  |
| **Selective Hydration** | Large apps where only some parts must be interactive fast | Amazon product page (buy button first) |

| Step                              | CSR (Client)                  | SSR (Server)                 | Streaming SSR                            | Selective Hydration                                  |
| --------------------------------- | ----------------------------- | ---------------------------- | ---------------------------------------- | ---------------------------------------------------- |
| **Initial HTML**                  | Empty `<div id="root"></div>` | Fully built HTML             | Partial HTML (sent in chunks)            | Fully built HTML (same as SSR/Streaming)             |
| **Who Builds UI?**                | Browser (JS runs)             | Server                       | Server (but streams gradually)           | Server (same as SSR/Streaming)                       |
| **When Page Appears**             | After JS loads + runs         | Immediately (HTML is ready)  | Immediately (skeleton), then chunks fill | Immediately (HTML ready)                             |
| **When Page Becomes Interactive** | After hydration of whole app  | After hydration of whole app | After hydration of whole app             | Parts hydrate **on priority** (critical parts first) |

## ✅ Wrap-Up Analogy

* **CSR** = Restaurant gives you **raw ingredients** → you cook on your table. (slow start, fun later)
* **SSR** = The Restaurant serves you a **fully cooked meal**. (fast start, but the kitchen is busy)
* **Streaming** = They serve **starter → main course → dessert** as they’re ready. (progressive experience)
* **Selective Hydration** = They serve **main dishes first**, smaller sides later. (critical first, rest later)

***

Still confused between? Streaming SSR and Selective Hydration?

<table><thead><tr><th width="146.60000610351562">Feature</th><th>Streaming SSR</th><th>Selective Hydration</th></tr></thead><tbody><tr><td><strong>When used</strong></td><td>Server is slow / lots of data to fetch</td><td>Page has many components, hydration is heavy</td></tr><tr><td><strong>Goal</strong></td><td>Show UI earlier (visual progress)</td><td>Make UI interactive earlier (critical first)</td></tr><tr><td><strong>What improves?</strong></td><td>Time to First Paint (see something)</td><td>Time to First Interaction (click something)</td></tr><tr><td><strong>Who controls order?</strong></td><td>Server decides which chunks to send first</td><td>Browser/React decides which components hydrate first</td></tr><tr><td><strong>Analogy</strong></td><td>Restaurant serves food course by course</td><td>Restaurant makes main dish edible first, sides later</td></tr></tbody></table>
