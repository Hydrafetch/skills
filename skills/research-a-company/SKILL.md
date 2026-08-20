---
name: research-a-company
description: "Build a company profile from its domain: identity, logos and colours, positioning, and site structure. Use for enrichment, competitor research, or answering what a company does."
license: MIT
---

# Skill: Research a company from its domain

## What this skill does

Builds a picture of a company from nothing but its domain: who they are, what they look like, what they sell, and what they have published. Uses purpose-built endpoints where they exist rather than scraping and guessing.

## When to use it

- "What does acme.com do?"
- Enriching a lead, an account or a CRM record
- Sizing up a competitor
- You need a company's logo, colours or positioning

## The sequence

Work in this order and stop as soon as you have what was asked for. Each step costs credits, so do not run all four out of habit.

**1. Identity — one call, and often enough.**

```
GET https://api.hydrafetch.com/v1/web/brand?domain=acme.com
X-API-Key: $HYDRAFETCH_API_KEY
```

Returns name, description, logos that suit light and dark backgrounds, the real brand colours, and social profiles. Every field states whether the company declared it or we harvested it. This answers "who is this company" far better than a scrape of their homepage, and costs 5 credits.

**2. Positioning — scrape the pages that carry it.**

Homepage for the pitch, `/pricing` for the model, `/about` for the story. One credit each.

```
POST https://api.hydrafetch.com/v1/web/scrape
{"url": "https://acme.com/pricing", "preferStructure": true}
```

Use `preferStructure: true` here: pricing and product pages carry meaning in their tables and headings.

**3. Structure — only when you do not know which pages exist.**

```
POST https://api.hydrafetch.com/v1/web/map
{"url": "https://acme.com", "limit": 200}
```

Returns their URLs without scraping them, one credit. Read the list, pick the handful worth reading, then scrape those. Do not crawl the whole site to answer a question about the company.

**4. Design system — only if you are rendering something in their brand.**

```
GET https://api.hydrafetch.com/v1/web/styleguide?domain=acme.com
```

Colours by role with contrast ratios, the type scale, corner radius and button styling, read from computed styles in a real browser. 10 credits, so do not call it for a description.

## Budgeting

A thorough profile is typically brand (5) plus three scrapes (3) plus a map (1): 9 credits. Adding the styleguide doubles it. If the user only asked what a company does, step 1 alone is usually the honest answer.

## Do not

- Do not scrape a homepage to get a logo. `brand` returns real assets with dimensions and background suitability.
- Do not crawl an entire site for a summary.
- Do not infer a company's colours from a screenshot. `styleguide` returns the resolved hex values.
