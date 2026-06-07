---
icon: coconut
---

# Nuxt Deep Dive...

<details>

<summary>How does Nuxt.js handle static site generation, and when would you choose it over server-side rendering?</summary>

Static site generation (SSG) in Nuxt.js pre-renders pages at build time, creating static HTML files that can be served directly from a CDN. This approach is ideal for content-heavy sites that don't require real-time data updates.

> SSG offers benefits like improved performance, better SEO, and reduced server load
>
> SSG is preferable, such as for blogs, documentation sites, or marketing pages.

#### Use SSR when

* Data changes frequently or is user-specific (dashboards, feeds)
* Personalised / authenticated content per request
* SEO needed on dynamic content (e-commerce product pages)
* You can't afford stale data from a build-time snapshot

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>



</details>
