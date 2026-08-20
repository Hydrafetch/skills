---
name: extract-structured-data
description: "Pull typed, schema-shaped JSON out of web pages. Use when you need fields rather than prose, or the same fields from many pages."
license: MIT
---

# Skill: Extract typed data from pages

## What this skill does

Pulls typed, schema-shaped JSON out of one or more web pages, so you get fields you can rely on rather than prose you have to parse.

## When to use it

- You need the same fields from many pages
- The answer is a value, not a passage: a price, a date, a headcount, a list of features
- You are filling a record, a table or a database row

## How to call it

```
POST https://api.hydrafetch.com/v1/web/extract
X-API-Key: $HYDRAFETCH_API_KEY
Content-Type: application/json

{
  "urls": ["https://acme.com/pricing"],
  "schema": {
    "type": "object",
    "properties": {
      "currency": {"type": "string"},
      "plans": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "name": {"type": "string"},
            "monthlyUsd": {"type": "number"}
          }
        }
      }
    }
  }
}
```

Over MCP, call the `extract` tool.

## Writing the schema

The schema is the instruction. A vague schema produces vague output.

- Name fields the way the page does. `monthlyUsd` beats `price` when the page shows several prices.
- Type numbers as numbers so you get `29` rather than `"$29/mo"`.
- Mark what you actually need with `required`; leave the rest optional so a page missing one field still returns the others.
- Prefer a flat shape. Deeply nested schemas are harder for the model and harder for you to consume.
- Add a `prompt` alongside the schema when a field needs judgement: "monthlyUsd is the price when billed monthly, not the discounted annual rate."

## Before you extract

Extraction runs a model over the page, so it costs more than a scrape: 5 credits per URL against 1. If you only need one value from one page, scrape it and read the value yourself.

Check the page has the data first. Extracting from a JavaScript shell returns nulls and still costs credits.

## Reading the response

Each URL comes back with its own result. A `null` means the model could not find the field, not that the call failed. Nulls across every URL usually mean the schema does not match what the pages say; change the schema rather than retrying.

## Do not

- Do not extract when the page publishes structured data already. Scrape with `formats: ["structured"]` first and read its JSON-LD, which is authored by the site and costs 1 credit.
- Do not send 50 URLs to find out whether your schema works. Try one.
