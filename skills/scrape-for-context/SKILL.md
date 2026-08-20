---
name: scrape-for-context
description: "Turn one URL into clean markdown for a model to read. Use when a user gives you a link, when you need a page as grounding, or when a previous fetch returned a JavaScript shell or a cookie wall."
license: MIT
---

# Skill: Scrape a page for LLM context

## What this skill does

Turns a URL into clean markdown suitable for putting in a model's context: navigation, banners, cookie notices and boilerplate removed, the page's own structured data available alongside it. Handles the fetch strategy for you, including pages that only render under JavaScript and pages behind bot protection.

## When to use it

- A user gives you a URL and asks what it says
- You need a documentation page, article or reference as grounding
- A previous fetch returned an empty page, a cookie wall, or a JavaScript shell

## How to call it

```
POST https://api.hydrafetch.com/v1/web/scrape
X-API-Key: $HYDRAFETCH_API_KEY
Content-Type: application/json

{"url": "https://example.com/article"}
```

Over MCP, call the `scrape` tool with the same arguments.

## Choosing options

Defaults are tuned for reading, so start with none.

- `preferStructure: true` when the layout carries meaning: pricing tables, comparison grids, API references, spec sheets. The default optimises for content density and can flatten a marketing or listing page into prose.
- `includeLinks: true` when you need to follow links or cite them. Off by default, because dropping link markup is what keeps the text dense; a page's links are also available on their own via `formats: ["links"]`.
- `formats: ["markdown", "structured"]` when the page publishes JSON-LD, microdata or OpenGraph. Structured data is authored by the site, so prefer it over parsing prose when both answer the question.
- `maxAge` in milliseconds to accept a cached copy. Cached responses cost nothing and return immediately.

## Reading the response

`data.markdown` is the content. `data.metadata` carries title, description, word count and a `format` field telling you which extractor ran. `data.quality.confidence` is how sure we are the extraction is complete.

A low confidence with a short `wordCount` usually means the page really is short, not that extraction failed. Retry with `preferStructure: true` before concluding the page is empty.

## Cost

One credit per page, whatever it took to fetch. A page that needed a browser render or an unblocker costs the same as one that came back on the first try. Failed requests are never billed.

## Do not

- Do not retry a 4xx. Fix the request instead.
- Do not fan out across many URLs to work around a rate limit; use `batch` for volume.
- Do not scrape a site's pages one by one to build a picture of a company. Use the `research-a-company` skill.
