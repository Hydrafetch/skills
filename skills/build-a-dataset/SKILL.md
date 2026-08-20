---
name: build-a-dataset
description: "Assemble many pages into a table using queued crawl and batch jobs rather than a loop. Use for corpora, bulk enrichment, or any job over more than a handful of URLs."
license: MIT
---

# Skill: Build a dataset from the web

## What this skill does

Turns a question into a table: find the pages, fetch them at volume, and pull the same fields from each. Uses queued jobs rather than a loop, so hundreds or thousands of pages are one call and one poll.

## When to use it

- "Get me every X on this site"
- Assembling a corpus for analysis, indexing or fine-tuning
- Any job where you would otherwise write a for-loop over URLs

## The sequence

**1. Find the URLs.**

If they are all on one site:

```
POST https://api.hydrafetch.com/v1/web/map
{"url": "https://example.com", "limit": 5000}
```

One credit, returns URLs without fetching them. Filter the list yourself before spending anything on content.

If you do not know the sites:

```
POST https://api.hydrafetch.com/v1/web/search
{"query": "your question", "limit": 20}
```

Results come back already scraped: 1 credit for the search plus 1 per result.

**2. Fetch at volume.**

For a known list of URLs, use batch rather than looping over scrape:

```
POST https://api.hydrafetch.com/v1/web/batch
{"urls": ["...", "..."], "formats": ["markdown"]}
```

To walk a site you have not enumerated, use crawl:

```
POST https://api.hydrafetch.com/v1/web/crawl
{"url": "https://example.com", "limit": 500}
```

Both return a job id. Poll `GET /v1/web/batch/{id}` or `GET /v1/web/crawl/{id}` until status is `completed`. Both are one credit per page, and pages that fail are not billed.

If the user has a webhook configured, deliveries are pushed instead and you do not poll at all.

**3. Type the rows, if you need fields rather than text.**

Feed the URLs that came back into `extract` with a schema. See the `extract-structured-data` skill. This is the expensive step at 5 credits a URL, so filter first: extract from the 200 pages that matter, not the 5000 you fetched.

## Budgeting

State the cost before you start a large job. A 5,000 page crawl is 5,000 credits; extracting from all of them is another 25,000. Map first, filter, then spend.

Check the balance if you are unsure. Every response carries `usage.creditsRemaining`.

## Handling long jobs

Crawls and batches run for minutes, not seconds. Poll with backoff rather than in a tight loop, tell the user it is running, and do not start a second job because the first has not finished.

## Do not

- Do not loop `scrape` over a URL list. Batch exists, is the same price, and is far faster.
- Do not crawl without a `limit`. Set one you have budgeted for.
- Do not re-fetch pages you already have. Pass `maxAge` to accept a cached copy for free.
