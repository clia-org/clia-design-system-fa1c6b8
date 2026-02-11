# Thread closing protocol

Intent: define the minimal, durable data required to close a thread (mission) while keeping
computed views (report cards, totals, dashboards) deterministic.

## Thread files

A thread lives at:

- `threads/<slug>/<slug>.thread.clia.json`
- `threads/<slug>/events.thread.clia.jsonl`
- `threads/<slug>/contributions.thread.clia.jsonl`

## Closing requirements (2.5-of-3)

Closing a thread should be strict but not brittle.

### Hard requirement (always)

**`<slug>.thread.clia.json` is required**.

It is the canonical snapshot of:

- `status: open|closed`
- intent / definitionOfDone
- timestamps
- `close` (closedAt, closedBy, reason, summary)

### Soft requirements (default required; waive-able)

- `events.thread.clia.jsonl` should exist and include at least:
  - `thread.created`
  - `thread.closed`
- `contributions.thread.clia.jsonl` should exist and include at least one contribution record
  **if** the thread should count toward contributor tracking/rewards.

## Waivers (stored as data)

When a soft requirement is skipped, record an explicit waiver as an event so audits remain
transparent.

Example waiver event:

```json
{"schemaVersion":"thread.event.v0.1","ts":"...","slug":"...","type":"thread.close.waiver","by":"rismay","waived":["contributions"],"reason":"No-credit administrative thread"}
```

## Minimum viable close set

### Small thread

- mission: closed ✅
- events: created + closed ✅
- contributions: waived ✅

### Mission-complete thread

- mission: closed ✅
- events: includes evidence/spinoffs/decisions ✅
- contributions: recorded by actor ✅

## Recommended lint rules

- Mission must be closed to consider the thread closed.
- If `events.thread...` exists, it must include `thread.created` and `thread.closed`.
- If `contributions.thread...` exists, it must be non-empty (or a waiver must exist).

