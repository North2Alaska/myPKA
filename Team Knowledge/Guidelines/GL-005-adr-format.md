# GL-005 - ADR Format

> **This Guideline defines the format, location, and lifecycle of Architecture Decision Records (ADRs) in myPKA.** SOPs and Workstreams that produce ADRs `[[wikilink]]` here rather than restating the rules.

ADRs live **next to the document they belong to** — every grilled or designed plan has its own per-document ADR folder. ADRs are *records about specific decisions*; they are not Team Knowledge. Team Knowledge holds the rules (Guidelines, SOPs, Workstreams); ADRs hold the *why we chose this once*.

See [[SOP-011-grill-with-docs]] for the procedure that produces ADRs.

## Where ADRs live

Pattern: every target document at `<path>/<slug>.md` may have a sibling folder `<path>/<slug>-adr/` containing its ADRs.

```
<any-path>/
├── my-plan.md              ← target document; ## Decisions section captures all decisions
└── my-plan-adr/            ← created lazily, only when first ADR is promoted
    ├── ADR-001-event-sourced-orders.md
    └── ADR-002-postgres-write-model.md
```

- **Folder name:** target file's slug plus the `-adr` suffix, all lowercase per [[GL-001-file-naming-conventions]] §1.
- **ADR filename:** `ADR-NNN-<slug>.md`, three-digit zero-padded, no skips on write. Numbering resets per document — each doc's ADRs start at `001`.
- **Lazy creation.** The folder is created only when an actual ADR is promoted out of the target document's `## Decisions` section. Don't pre-create empty `-adr/` folders.

## Template

```md
# {Short title of the decision}

{1-3 sentences: what's the context, what did we decide, and why.}
```

That's it. An ADR can be a single paragraph. The value is in recording **that** a decision was made and **why** — not in filling out sections.

## Optional sections

Only include these when they add genuine value. Most ADRs won't need them.

- **Status** frontmatter (`proposed | accepted | deprecated | superseded by ADR-NNN`) — useful when decisions are revisited.
- **Considered Options** — only when the rejected alternatives are worth remembering.
- **Consequences** — only when non-obvious downstream effects need to be called out.

## When to offer an ADR

All three of these must be true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful.
2. **Surprising without context** — a future reader will look at the code or the plan and wonder "why on earth did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons.

If a decision is easy to reverse, skip it — you'll just reverse it. If it's not surprising, nobody will wonder why. If there was no real alternative, there's nothing to record beyond "we did the obvious thing."

When in doubt, leave the decision inline in the target file's `## Decisions` section. The ADR bar is intentionally high.

## What qualifies

- **Architectural shape.** "We're using a monorepo." "The write model is event-sourced, the read model is projected into Postgres."
- **Integration patterns between contexts.** "Ordering and Billing communicate via domain events, not synchronous HTTP."
- **Technology choices that carry lock-in.** Database, message bus, auth provider, deployment target. Not every library — just the ones that would take a quarter to swap out.
- **Boundary and scope decisions.** "Customer data is owned by the Customer context; other contexts reference it by ID only." The explicit no-s are as valuable as the yes-s.
- **Deliberate deviations from the obvious path.** "We're using manual SQL instead of an ORM because X." Anything where a reasonable reader would assume the opposite. These stop the next engineer from "fixing" something that was deliberate.
- **Constraints not visible in the code.** "We can't use AWS because of compliance requirements." "Response times must be under 200ms because of the partner API contract."
- **Rejected alternatives when the rejection is non-obvious.** If you considered GraphQL and picked REST for subtle reasons, record it — otherwise someone will suggest GraphQL again in six months.

## Lifecycle

- **Accepted** is the default and usually only state. Most ADRs don't need a status field at all.
- **Deprecated** when a later ADR overrules it but the original context still has value to readers (e.g., "we used to do X — here's why we don't now"). Add the status line; leave the body intact.
- **Superseded by ADR-NNN** when a newer ADR in the same `-adr/` folder replaces this one. Keep the original ADR file in place; do not delete. Update the original with the supersession line at the top.
- **Numbering on retirement.** Match [[GL-001-file-naming-conventions]] §6 — gaps from retired ADRs are acceptable, and reusing a retired number requires logging the retirement in a session log first.

## Updates to this Guideline

If the format changes, update this file. Do not duplicate the change into [[SOP-011-grill-with-docs]] or any other consumer — they `[[wikilink]]` here and inherit the change automatically.
