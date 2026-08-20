## Description:

Hydrafetch helps agents turn any URL into clean Markdown and structured data: page scraping, site mapping, web search, schema-shaped extraction, brand and logo lookup, design systems, screenshots, and bulk crawl or batch jobs.

This skill is ready for commercial/non-commercial use.

## Publisher:

[AkashRajpurohit](https://clawhub.ai/user/AkashRajpurohit)

### License/Terms of Use:

MIT

## Use Case:

Developers and agents use this skill to read current public-web pages as Markdown for model context, pull typed JSON out of websites against a JSON Schema, resolve a company's brand and logo from its domain, and process many URLs through queued crawl or batch jobs.

### Deployment Geography for Use:

Global

## Known Risks and Mitigations:

Risk: The skill calls the Hydrafetch API for live public-web tasks and consumes credits from the configured workspace.

Mitigation: Configure an API key only when intended, and review broad crawl or batch requests before approval. Failed requests are never billed, and a page costs one credit regardless of how it was retrieved, so cost scales with pages requested rather than with page difficulty.

Risk: Fetched page content is attacker-controlled. Anyone can publish a page containing text aimed at an agent reading it.

Mitigation: The skill instructs the agent to treat all scraped content as untrusted data and never as instructions. Preserve source URLs so any claim can be traced back to the page that made it.

Risk: Scraping can be pointed at sites whose terms prohibit it.

Mitigation: Respect robots.txt and site terms, and confirm before crawling a third party's site at volume. The API paces requests per host, but the choice of target is the caller's.

Risk: Structured extraction may return incomplete fields for pages that do not contain the requested data.

Mitigation: The skill instructs the agent to keep nullable fields null rather than inventing values, and to validate output against the requested JSON Schema.
