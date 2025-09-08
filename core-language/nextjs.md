# NextJS

<details>

<summary>Why Only the First Page is Truly Server-Side Render in Next.js? Rest of the page actually render in client side.</summary>

একটা অবাক করা ব্যাপার হলো — Next.js-এ আপনি যতই পেজ server-side render করুন না কেন, আপনার হিট করা শুধুমাত্র প্রথম পেজটাই server-side render হয়। বাকি সব পেজ ঠিকই client-side এ render হয়!

অনেকেই মনে করেন, “আমি তো সব পেজেই getServerSideProps দিয়েছি, তাহলে তো সব পেজই SSR হওয়া উচিত!”\
কিন্তু বাস্তবতা হলো — আপনি যে পেজে প্রথমবার ঢুকছেন, শুধু সেই পেজের HTML server থেকে পাঠানো হয়। বাকি পেজগুলোতে আপনি দিয়ে গেলে, Next.js backend থেকে .rsc বা .json ফর্মে data এনে client-side এ render করে।

বর্তমানে অনেক ডেভেলপার Next.js দিয়ে কাজ করলেও এর ভিতরের rendering behavior ঠিকভাবে বোঝে না — বিশেষ করে যেটা আমি বুঝেছি: প্রথম পেজে server থেকে HTML আসে, কিন্তু বাকি পেজে গেলে শুধু .rsc বা virtual DOM এর মতো ডেটা আসে — HTML না। কেন?

আমিও শুরুতে মনে করতাম, সব পেজেই যদি server থেকে HTML পাঠানো হতো, তাহলে SEO আরও ভালো হতো। কিন্তু, বাস্তবতা অনেক গভীর এবং চমকপ্রদ!

চলুন ব্যাখ্যা করি:\
Next.js মূলত একটি Hybrid Rendering Framework — এটি intelligent ভাবে বেছে নেয় কখন SSR (Server-Side Rendering), কখন SSG (Static Site Generation), আর কখন CSR (Client-Side Rendering) হবে।

যখন একজন user প্রথমবার কোনো পেজে ঢুকে, Next.js সেই পেজের HTML server থেকে পাঠায় — এতে SEO ও performance দুটোই বজায় থাকে।

কিন্তু এরপর যদি user ব্যবহার করে অন্য পেজে যায়, Next.js তখন আর পুরো HTML পাঠায় না। পাঠায় .rsc, .json, বা component-level data — যেটা browser নিজে render করে client-side এ। এটা হয়:\
SPA feel দেওয়ার জন্য (reload ছাড়া smooth navigation)\
Performance boost করার জন্য (HTML heavy নয়, শুধু প্রয়োজনীয় data)\
এবং Server load কমানোর জন্য

তাহলে প্রশ্ন: এটা কি SEO-তে ক্ষতি করে?\
না। Google বা অন্যান্য SEO bot সাধারণত প্রথমবার যেই পেজে ঢুকছে, শুধু সেটাকেই crawl করে। তারা JavaScript execution করে না, internal navigation follow করে না।\
তাই যদি প্রতিটি SEO-critical পেজ (যেমন homepage, blog, product details) SSR বা SSG করা থাকে, তাহলে SEO damage হয় না — বরং performance বাড়ে।

এই Smart Rendering System এর কারণেই Next.js এখনকার দিনে সবচেয়ে শক্তিশালী React Framework গুলোর একটি।\
তবে, যদি কারও প্রজেক্টে এমন প্রয়োজন হয় যে, প্রত্যেকটি পেজেই server-rendered HTML লাগবে (যেমন গ্লোবাল নিউজ পোর্টাল বা government portal) — তখন Next.js সেই ফ্লেক্সিবিলিটিও দেয়:\
getServerSideProps বা App Router-এ dynamic = "force-dynamic" দিয়ে।

একজন ডেভেলপার হিসেবে আমার অভিজ্ঞতা বলছে:\
Next.js শিখতে শুধু component বানানোই যথেষ্ট নয়, বরং এর architecture, rendering pipeline, এবং server-client responsibility deeply বোঝা খুব দরকার — বিশেষ করে যারা Performance, SEO, এবং Scalability-র দিকে ফোকাস করতে চায়।

Author : [Tanver Ahmed](https://www.linkedin.com/in/tanver-js/overlay/about-this-profile/)&#x20;

</details>



