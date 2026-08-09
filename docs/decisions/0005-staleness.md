# 0005 — Staleness: the publisher declares a lifetime, the consumer declares a tolerance, the stricter wins

- **Status:** proposed
- **Date:** 2026-08-09
- **Resolves:** SPECIFICATION.md §5 question 2
- **Proposed by:** the voicegent architect

## Context

R2 makes `measurement.at` mandatory and says a consumer *may* reject a document older than a
threshold it sets. The open question was whether N is the consumer's choice or the publisher's.

## Decision

**Both, and they are different facts. The effective limit is the stricter of the two.**

```yaml
# provides — the PUBLISHER, who knows how fast this host changes
measurement:
  at: 2026-08-09T00:14:52Z
  by: https://github.com/o/r/actions/runs/31270809170
  valid_for: 7d                    # or `immutable`

# requires — the CONSUMER, who knows its own risk tolerance
max_provides_age: 24h              # optional
```

`effective = min(valid_for, max_provides_age)`. Exceeding it is a **`SKIP`, and `SKIP` blocks** (R3) —
an expired measurement is not a false claim, it is an unknown one, and the distinction is the whole
of R3.

## Why this one

**Neither party holds both halves of the fact.** The publisher knows the host's mutation rate: a bare
host running unattended-upgrades can gain a package overnight; a container image identified by digest
cannot change at all. The consumer knows what a wrong answer costs it: a lane that only reads a
version tolerates a week-old document, a lane about to run a migration under a privilege claim does
not. A single N imposed by either party is wrong for the other's case.

**And staleness is a property of the IDENTITY kind, which R4 already established.** This is the part
worth stating in the spec rather than leaving to implementers:

> A `provides` document identified by `image_digest` describes an artifact that is immutable by
> construction. It cannot go stale in the way a document identified by a hostname can.

So `valid_for: immutable` is legitimate and correct **only when `identity.image_digest` is present**,
and a checker should reject `immutable` on a bare-host document rather than honour it. That is one
rule that removes a whole class of over-declaration — and it falls straight out of R4 rather than
being new policy.

## Why not a single spec-wide default

A default N in the specification would be the thing every consumer inherits without deciding, which
makes it folklore of exactly the kind R2's rationale warns about — *"it hardens into folklore and gets
built upon in both directions."* Omitting both fields should mean **no expiry check**, stated
explicitly, so that a consumer who wants one has made a choice rather than accepted a number nobody
chose.

## Consequences

- An immutable-digest document needs no re-measurement, which is the strongest argument for probing at
  **image build time** rather than per job: the measurement is as durable as the artifact it describes.
- A bare-host document is only as good as its last run, which argues for a scheduled probe on any host
  that is not fully image-defined. That is an operational consequence worth naming, not a spec rule.
- `hutch check` should print the effective limit and which side set it, per R5 — *"expired 3d ago;
  limit 24h set by requires"* names the owner of the next action, where *"stale"* does not.
