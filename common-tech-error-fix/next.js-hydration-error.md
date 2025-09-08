# Next.js এ Hydration Error

📍Next.js এ Hydration Error — কেন হয় এবং কীভাবে সমাধান করবেন?\
Next.js এ আমরা যারা কাজ করি তারা প্রায়ই এই এররটার সম্মুখীন হই:\
\
✅ এর মানে হলো, সার্ভার থেকে আসা HTML এবং ক্লায়েন্টের React render ঠিকমতো মিলে যাচ্ছে না।\
\
চলুন ধাপে ধাপে দেখি কেন হয় এবং কীভাবে সমাধান করবেন, যেন ভবিষ্যতে আর এই সমস্যা না হয়।\
\
📍কেন Hydration Error হয়?\
১️. Render-এ Dynamic Values ব্যবহার করা\
যেমন:\
\<div>{[Date.now](http://date.now/)()}\</div>\
\<div>{Math.random()}\</div>\
➡প্রতিবার নতুন মান আসায় server & client এর HTML mismatch হয়।\
\
২. Render-এ typeof window logic ব্যবহার\
যেমন:\
if (typeof window !== 'undefined') {\
// কোনো DOM logic\
}\
➡ সার্ভারে window থাকে না, তাই server-side এবং client-side এর মধ্যে পার্থক্য তৈরি হয়।\
\
৩️. Client-specific data ব্যবহার\
যেমন:\
const theme = localStorage.getItem("theme");\
➡ সার্ভারে localStorage নেই, তাই সমস্যা হয়।\
\
৪️. User locale-based date/time format\
যেমন:\
\
new Date().toLocaleDateString()\
➡User এর ব্রাউজারের locale অনুযায়ী তারিখ আলাদা হতে পারে।\
\
৫️. Invalid HTML nesting\
যেমন:\
\<div>\<p>\</div>\</p>\
➡️ ভুল nesting এর কারণে React hydrate করতে গিয়ে mismatch পায়।\
\
৬️. Fonts বা CSS class mismatch\
Custom font বা CSS module এর dynamic class name সার্ভারে একরকম, ক্লায়েন্টে অন্যরকম হতে পারে।\
\
✅সমাধান (How to Fix)\
১️. Dynamic values সরিয়ে ফেলুন বা useEffect ব্যবহার করুন\
❌ \<div>{[Date.now](http://date.now/)()}\</div>\
\
✅ const \[now, setNow] = useState(null);\
\
useEffect(() => {\
setNow([Date.now](http://date.now/)());\
}, \[]);\
\
if (!now) return null;\
\
return \<div>{now}\</div>;\
২️. typeof window logic সব সময় useEffect এ রাখুন\
useEffect(() => {\
if (typeof window !== 'undefined') {\
// client-side logic\
}\
}, \[]);\
৩️. Client-specific data fetch করলে, তা useEffect এ নিন\
const \[theme, setTheme] = useState("light");\
\
useEffect(() => {\
const localTheme = localStorage.getItem("theme");\
setTheme(localTheme || "light");\
}, \[]);\
৪️. Client-only component হলে SSR বন্ধ করুন\
import dynamic from 'next/dynamic';\
const ClientComponent = dynamic(() => import('./ClientComponent'), { ssr: false });\
৫️. HTML nesting সবসময় সঠিক রাখুন\
❎ \<div>\<p>\</div>\</p>\
✅৷ \<div>\<p>\</p>\</div>\
৬️. Fonts ও CSS module এর configuration সঠিকভাবে করুন\
Next.js এ @next/font বা অন্যান্য সঠিক পদ্ধতি ব্যবহার করুন।\
\
Dynamic class name বা font mismatch যেন না হয় তা নিশ্চিত করুন।\
\
📍ভবিষ্যতে যাতে Hydration Error না হয়, কী কী খেয়াল রাখবেন?\
✅ Render-এ কখনো random বা dynamic value ব্যবহার করবেন না।\
✅ Client-only logic সব সময় useEffect এ রাখুন।\
✅ Client-specific data server-side এ আনুন বা useEffect এ নিন।\
✅ HTML nesting ঠিক রাখুন।\
✅ Fonts ও CSS এর config ঠিক রাখুন।\
✅ দরকার হলে SSR বন্ধ করুন।\
\
📍 শেষ কথা\
Hydration error আমাদের প্রজেক্টের UI কে ভাঙা এবং ইউজারের জন্য খারাপ অভিজ্ঞতা দেয়।\
সঠিক নিয়ম মেনে কোড লিখলে খুব সহজেই এড়ানো যায়।
