# 0006 — Multi-host selection: derive the host from the lane's own `runs-on`, never restate it

- **Status:** accepted (ratified 2026-08-09, see [`0010`](0010-ratification-of-0004-0009.md))
- **Date:** 2026-08-09
- **Resolves:** SPECIFICATION.md §5 question 3
- **Proposed by:** the voicegent architect

## Context

A repo whose lanes run on several hosts needs the checker to select the right `provides`. Two options
were on the table: select by label, or by an explicit reference in `requires`.

## Decision

**Derive it from the lane's own `runs-on` labels. A `requires` document does not name its host.**

`hutch check` reads the workflow's `runs-on` for the named lane, matches those labels against each
candidate `provides` document's `identity`, and:

- **exactly one match** → check against it
- **zero matches** → `SKIP` (blocks)
- **more than one match** → `SKIP` (blocks)

Multiple matches are a genuine ambiguity — two hosts answering the same label set may differ in what
they supply — and R3 says an ambiguity blocks rather than picking one.

## Why this one

**An explicit host reference in `requires` duplicates a fact the workflow already states, and the two
will drift.** That is the failure this entire project was created to prevent, reproduced inside its
own configuration format. The lane's `runs-on` is not merely *a* statement of where it runs — it is
the statement the scheduler obeys. Any second copy is a claim about scheduling that the scheduler does
not read, and when they disagree the checker verifies a host the job never lands on. **That is worse
than no check: a green `hutch check` against the wrong `provides` is a confident wrong answer, which
is the shape of all six failures in `WHY.md`.**

**§4 already decided this and the answer follows from it.** The spec puts allocation out of scope
because it *"belongs in whatever the fleet already uses to declare it."* Host binding is allocation.
Having excluded it, `hutch` must READ it rather than ask for it again.

**Selecting "by label" and deriving from `runs-on` are the same mechanism** — the difference is only
whether the labels are re-typed into a hutch document. They should not be.

## The constraint this puts on `provides.identity`

Selection needs `identity` to be matchable against label sets, so `identity` must carry the labels the
host registers under, and they must be **measured or read from the fleet's own declaration, not typed
into the probe**:

```yaml
identity:
  id: sasha-ubuntu
  labels: [self-hosted, sasha-ubuntu, build]   # as REGISTERED, not as intended
  image_digest: sha256:abc123...
```

A registered label set that differs from the intended one is itself a finding worth surfacing, and it
is invisible if the probe writes down what it believes rather than what the runner registered.

## Consequences

- The checker needs to parse enough workflow YAML to resolve `runs-on` for a named lane, including
  matrix expansion. That is real work and it is the cost of not having a second source of truth.
- A lane whose `runs-on` is a bare `ubuntu-latest` has no self-hosted `provides` and should `SKIP`
  rather than silently pass — hosted-runner capabilities are exactly the accidental ones §0 names.
- Consumers get a free consistency check: a `requires` for a lane that no workflow defines is a
  dangling document, and the checker can say so.
