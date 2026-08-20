---
name: show-a-company-logo
description: "Render a real company logo from a domain: an embeddable image URL for the browser, or JSON for a server. Use for CRM records, lead lists, directories, integration pages, or anywhere a company name currently sits next to a coloured initial."
license: MIT
---

# Skill: Show a company logo

## What this skill does

Puts a real company logo next to a company name, from nothing but a domain. This is the most common thing anyone needs from company data and the least interesting to build: favicon scraping, an `og:image` that turns out to be a hero shot, a fallback that renders a grey square, and a logo that is invisible the moment someone turns on dark mode.

Two ways in. Pick by whether a browser or your server is asking.

## In a browser: use the image URL

```
https://img.hydrafetch.com/logo/{domain}?token={publishable_key}
```

That is a complete image URL. It goes straight in an `<img>` tag, a CSS `background-image`, an email, or a spreadsheet cell. There is no fetch, no JSON, no await.

```html
<img src="https://img.hydrafetch.com/logo/stripe.com?token=hf_pk_...&size=64"
     alt="Stripe" width="32" height="32" />
```

The `hf_pk_` key is publishable by design. It is meant to be in page source, it is locked to the domains you register, and it cannot read anything else in the account. **Do not use a secret API key here** — if you find yourself proxying logo requests through your own backend to hide a key, you have the wrong key.

| Parameter | Values | Notes |
| --- | --- | --- |
| `size` | pixels | Longest edge. Rounded up to the nearest stored size. |
| `theme` | `light`, `dark`, `auto` | Which background the mark will sit on. |
| `type` | `icon`, `wordmark` | Symbol only, or the name as drawn. |
| `fallback` | `monogram`, `404`, `transparent` | What to serve when there is no logo. |

Set `referrerPolicy="strict-origin-when-cross-origin"` on the tag if your app sends `no-referrer`. Domain locking reads the referrer, and a stripped one looks like an unregistered origin.

## On a server: use the JSON endpoint

```
GET https://api.hydrafetch.com/v1/web/brand/logo?domain=stripe.com&theme=dark&type=icon
X-API-Key: {secret key}
```

Returns the URL plus what was chosen — format, dimensions, variant. One credit. Use this when you are storing a reference, populating a database column, deciding layout from the real dimensions, or working somewhere that has no browser.

## Choosing theme and type

**`theme` is the background the logo sits on, not the logo's own colour.** `light` means a light background, so it returns a mark that reads against one. Getting this backwards is the single most common way to end up with an invisible logo.

Use `auto` when the surface follows the viewer's system theme. Where a mark exists in only one colour, `auto` returns a version that adapts, so it stays visible in both.

`icon` for anything small or square — table rows, avatars, favicons, dense lists. `wordmark` when there is horizontal room and the name should be readable: logo walls, "trusted by" strips, invoice headers, slide footers.

## Fallbacks, which is where this usually goes wrong

Every logo API misses sometimes. The domain is new, the site is down, the company never published a mark worth using. What you serve then is a product decision, so make it deliberately.

- **`monogram`** (the default) returns a generated letter mark in the brand's colours. Best for user-facing lists, where a hole in the grid looks broken.
- **`404`** returns a real 404 so `onError` fires and your own placeholder renders. Best when you already have a component for this. Note that this means 404s in your logs are normal and expected.
- **`transparent`** returns a transparent pixel. Use when nothing at all is better than something wrong.

Whichever you pick, do not write a retry loop around a missing logo. A miss is a fact about that domain, not a transient failure, and hammering it changes nothing.

## Where it does not fit

- **A logo you already have.** If the customer uploaded one, use theirs. This is for companies you have not met.
- **Logos as proof of endorsement.** Rendering a company's mark on your marketing site to imply they are a customer is a trademark problem, and no API makes it not one.
- **Bulk-downloading marks to redistribute.** Serve them, do not warehouse them.
- **A person's avatar.** This resolves companies from domains. It is not a people API.
- **Somewhere a wrong logo is dangerous.** Anything financial, legal or safety-critical deserves a human-confirmed asset, not a resolved one.

## If you need more than the mark

`brand` returns the full record — name, description, colours, fonts, socials, every logo variant — for 5 credits. If you find yourself calling the logo endpoint and then scraping the same site for its colours, make one `brand` call instead.

## Rules

- Publishable key in the browser, secret key on the server. Never the reverse.
- `theme` describes the background, not the logo.
- Decide the fallback deliberately; the default is a monogram, not an error.
- Do not proxy the image endpoint through your own backend. That is what the publishable key exists to avoid.
- Do not retry a miss.
