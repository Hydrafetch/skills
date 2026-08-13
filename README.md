# Hydrafetch skills

Skills that teach an agent to use the [Hydrafetch](https://hydrafetch.com) web data API well: which endpoint answers which question, what each one costs, and what not to do.

Each skill is a single `SKILL.md` under `skills/`, in the format described by the [agent skills discovery schema](https://schemas.agentskills.io/discovery/0.2.0/schema.json).

| Skill | For |
| --- | --- |
| `scrape-for-context` | One page as clean markdown for a model to read |
| `research-a-company` | A company profile from its domain |
| `extract-structured-data` | Typed JSON out of pages, by schema |
| `build-a-dataset` | Many pages at volume, as a table |

They are published at `https://hydrafetch.com/.well-known/agent-skills/index.json`, with a `sha256` digest per file computed at publish time.

## Using them

Point an agent at the index, or copy a `SKILL.md` into whatever skills directory your client reads.

You need a Hydrafetch API key for anything in here to run. New workspaces get free credits with no card: https://app.hydrafetch.com

## Contributing

A skill earns its place by encoding a *sequence* or a *judgement*, not by restating one endpoint. `research-a-company` is a skill because the order matters and the cheap call usually suffices; "call the scrape endpoint" is not.

Keep the cost of each step visible. An agent spending someone else's credits should know what it is spending.
