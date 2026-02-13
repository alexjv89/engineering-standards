# Data Model Documentation

Document domain understanding, not schema.

## Principle

Model files already contain schema (fields, types, constraints). Documentation should add what code cannot express: domain semantics, conventions, and how models collaborate.

## What to Document

- **Domain semantics** — What fields mean in business terms, not just their types
- **Conventions** — Agreed patterns not enforced by code (e.g., metadata structure per entity type)
- **State machines** — Valid status transitions and what triggers them
- **Design rationale** — Why it's structured this way, tradeoffs considered
- **Model collaboration** — How data flows between related models

## What NOT to Document

- **Schema** — It's in the model file, don't duplicate
- **API endpoints** — Belongs in API documentation
- **CRUD examples** — Obvious from schema, adds no value
- **Code file paths** — Use search/grep

## Structure

Organize by **concept**, not by model. Group related models that work together.

**Bad** — One file per model:
```
docs/data-models/
├── orders.md
├── order-items.md
├── payments.md
├── shipments.md
```

**Good** — Organized by domain concept:
```
docs/data-models/
├── README.md        # Relationships diagram, overview
├── order-lifecycle.md   # Orders, OrderItems, Payments, Shipments as one flow
```

## Example

Instead of documenting `Orders`, `Payments`, `Shipments` separately:

```markdown
# Order Lifecycle

Orders flow through: draft → confirmed → paid → shipped → delivered

## Creation
Orders start as `draft`. Line items added via OrderItems.
`confirmed` when customer completes checkout.

## Payment
Payment creates linked Payments record.
Order → `paid` when payment.status = 'completed'.
Multiple partial payments supported (payment.amount < order.total).

## Fulfillment
Shipments record created when warehouse picks order.
Order → `shipped` when shipment.carrier_tracking populated.
Order → `delivered` when shipment.delivered_at set (webhook from carrier).

## Cancellation
Only `draft` and `confirmed` orders can cancel.
`paid` orders require refund flow first.
```

This tells the domain story. Schema details live in model files.

## When to Split

Split into multiple docs when:
- Domain concepts are genuinely independent
- File exceeds ~200 lines
- Different audiences need different sections

Keep together when:
- Models collaborate in a single flow
- Understanding one requires understanding others

## Related Notes

- [MECE](/principles/documentation/mece.md) — Organize without overlap or gaps
- [Single Idea Per Note](/principles/documentation/single-idea-per-note.md) — Atomic, focused notes
