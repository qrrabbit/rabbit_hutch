# 0002 — License

- **Status:** PROPOSED — open, owner's decision. **Not settled; no `LICENSE` file exists yet.**
- **Date:** 2026-08-09

## Context

The repository is public and intended to be adopted by other projects. Until a license is chosen,
"public" and "usable" are not the same thing: with no license, default copyright applies and nobody
may legally use, modify, or redistribute this — which quietly defeats the purpose of publishing it.

So this needs deciding before the repository is promoted anywhere, but it does not block writing the
specification.

## Options

**Apache-2.0 — recommended.**
- Includes an **express patent grant**, which MIT does not. For infrastructure that companies may
  adopt into their build pipelines, this is the thing corporate legal review looks for, and its
  absence is a common reason a small project gets quietly passed over.
- Requires preserving notices and stating changes — mild obligations, well understood.
- The default for infrastructure and specification projects for exactly these reasons.

**MIT — the alternative.**
- Shorter, more familiar, maximally permissive, near-zero friction for an individual contributor.
- No patent grant. For a checker and a file-format spec the practical patent risk is negligible, so
  this is a defensible choice rather than a wrong one.

**No license — not viable.** Excluded explicitly so it is not reached by default through inaction.
Doing nothing *is* choosing this one.

## Recommendation

**Apache-2.0**, on the grounds that the stated goal is adoption by other projects and organisations,
and Apache-2.0 removes a review obstacle that MIT leaves in place. If the priority shifts toward
maximum simplicity for individual contributors, MIT is a reasonable trade and nothing else in this
repository depends on which is chosen.

## Consequences

- Whichever is picked: add `LICENSE` at the repository root, name it in `README.md`, and flip this
  record to `accepted` with the date.
- Changing a license later requires the agreement of everyone who has contributed by then — so this
  is cheapest to settle now, while the contributor list is short.
