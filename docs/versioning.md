# Versioning

DCP uses Semantic Versioning at the protocol level. The goal is a long-lived
standard where old messages stay valid.

## Where the version lives

- `dcp_version` (e.g. `"1.0"`) is in every envelope. Parse it as **two integers**
  (`major`, `minor`) — never as a float, or `1.10` would sort below `1.9`.
- Schemas are namespaced by **major** version in their `$id`
  (`…/v1/task.schema.json`) and on disk (`schemas/v1/`).

## What may change within a major version (additive, optional only)

- New **optional** fields on existing objects.
- New values in **open** controlled vocabularies (`status`, `verb`, `priority`,
  `severity`, `impact_level`, `dependency_type`, `review_type`). Because these are
  open tokens, a new value does not break an older validator — consumers
  **tolerate and pass** unknown values.
- New examples, conformance cases, and documentation.

An older validator must keep accepting newer-but-compatible messages. That is the
compatibility promise.

## What requires a new major version (breaking)

- Removing or renaming a field, or changing its type.
- Changing the **closed** sets: `entity_type` (the eight nouns) or the `rel`
  vocabulary.
- Making an optional field required, or tightening a constraint in a way that
  rejects previously-valid messages.

Breaking changes ship as `v2` under a new namespace (`schemas/v2/`,
`…/v2/…`). `v1` remains valid and supported alongside it.

## Forward-compatibility rules for consumers

- Ignore unknown `extensions` (always optional, never a "must-understand").
- Tolerate and pass unknown verbs and unknown enumerated values.
- Do not derive meaning from identifiers beyond the prefix category.

## The `$id` host

`https://schemas.devcopro.org/v1/…` is the **canonical, permanent** namespace for
v1 schemas (SPEC §10.1). The domain is owned by the project and the v1 schema
files are served at these URLs (`application/schema+json`). These identifiers are
stable and will not be rebased within the v1 major version; a future major
version would use its own `…/v2/…` namespace. Consumers **SHOULD** prefer
bundled/cached schemas over live fetches.
