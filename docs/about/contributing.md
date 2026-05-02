# Contributing

This catalog is only useful if it stays current. Every architect on the team is expected to contribute.

## When to add a new pattern

- You completed a project that did not fit cleanly into an existing pattern
- You evaluated multiple stack options for a real project and want to record the result
- A technology you previously rated "Trial" or "Assess" has proven itself and should be promoted

## When to update an existing pattern

- The "Last reviewed" date is more than 12 months old
- A primary technology choice has been deprecated or superseded
- You discovered a new gotcha during a real project
- Costs have changed materially (vendor pricing changes, new free tiers)

## When to write an ADR

- A choice was non-obvious and other reasonable architects might choose differently
- The decision will be expensive to reverse
- You want future-you to remember why you didn't pick the "obvious" alternative

## How to contribute

1. Fork the repository
2. Copy `_template/pattern-template.md` or `_template/adr-template.md`
3. Fill it in
4. Open a pull request
5. At least one other architect must review and approve

## Style guide

- **Be opinionated.** "It depends" is rarely useful. Give a default, then explain when to deviate.
- **Cite real projects.** Pattern claims should link to a reference implementation (public preferred, anonymised internal acceptable).
- **Date everything.** Without a date, the reader cannot judge freshness.
- **Show alternatives.** This is a public, client-facing catalog. Vendor-neutral framing builds trust.
- **Keep diagrams in Mermaid.** Renders natively, version-controllable, no external tool required. Use draw.io only when Mermaid genuinely cannot express what you need; commit the `.drawio` source alongside an exported PNG.
