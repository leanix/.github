# LLM Wiki conventions

All wiki files live under this bundle directory.

The bundle follows the OKF v0.2 frontmatter contract in `schema.json`.
Committed knowledge lives under `wiki/`; raw source material and pending
operation state live under `raw/` and are ignored by default.

Reserved pages are `wiki/index.md` and `wiki/log.md`. Concept pages belong in
`wiki/concepts/`, convention pages in `wiki/conventions/`, query pages in
`wiki/queries/`, source provenance pages in `wiki/sources/`, and runbook pages
in `wiki/runbooks/`. The page types Service, Module, API, Datastore, Decision,
Insight, Reference, Policy, and Process also belong in `wiki/concepts/`.
