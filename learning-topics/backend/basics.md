# Basics



### Cross-Origin Resource Policy

<details>

<summary>If CORS protects your backend, how can Postman call your API, even without any CORS setup?</summary>

{% hint style="info" %}
We never configure Postman in our CORS settings, yet it works. 𝗪𝗵𝘆?
{% endhint %}

Like many developers, I had misunderstood what CORS actually does.

Here’s a simple but good example that helped me understand: Imagine this:

> You open your browser and visit your bank’s website — `bank.com` — and log in.> \
> The frontend makes API calls to `bank-api.com`, and your browser stores cookies.

Then, you open a new tab and visit a random site: `unknown-site.com`.

That site runs this script:

```
fetch(bank-api[dot]com/transfer?to=123&amount=3000, {
credentials: "include"
});
```

Since you're already logged in to bank.com, your browser has the cookies.\
And because credentials: "include" is used, the browser tries to attach those cookies to the request.

The backend receives it and thinks:

“This request is from an authenticated user, go ahead and transfer the money!”

💸 Boom. Money gone.

***

So, how does CORS help?

Because the request is coming from a different origin, the browser says:

> “Hey backend, this is from unknown-site.com. Should I allow it ?”

Your backend responds with:

```
Access-Control-Allow-Origin: bank[dot]com
```

Now, the browser checks and blocks the request, since unknown-site.com isn't allowed.

> ✅ CORS prevents cross-origin requests from untrusted sites.> \
> ❌ But it doesn’t protect your backend directly — the browser enforces it, not your server.

That’s why Postman works\
It’s not a browser, so CORS doesn’t apply.

If a user installs a browser extension that disables CORS, a malicious site could bypass CORS entirely.

Author : [MD Waliullah](https://www.linkedin.com/in/eng-waliullah?miniProfileUrn=urn%3Ali%3Afsd_profile%3AACoAAEIV20EBtZtsGyTcHEA86R8moy78fHJ5STQ)

</details>



