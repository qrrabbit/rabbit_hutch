# 0011 — Identity is only as declared as its least-declared layer

- **Status:** PROPOSED — for the fleet/image owner and the maintainer
- **Date:** 2026-08-09
- **Proposed by:** the OutBaking architect
- **Extends:** R4 (identity), [`0005`](0005-staleness.md) (staleness)
- **Found while:** the image owner answered [the psql question](../questions/psql-client-version.md);
  this is what their answer implies for the spec, not a defect they left behind

## Context

`Dockerfile:65` installed `postgresql-client` **unversioned**, on top of a base image pinned **by
digest**, under a comment insisting that a re-pin be deliberate and a tag never drift. One layer's
identity was rigorously declared; the next layer's was inherited from whatever the package archive
held that day.

That is not a lapse in care — the care is visibly present twenty-eight lines earlier. It is that
**"pinned" is not a property an artifact has; it is a property every layer of its derivation must
have**, and nothing makes the weakest layer visible.

## Why this reaches the spec, and not only that Dockerfile

`0005` allows `valid_for: immutable` on the reasoning that *"a document identified by `image_digest`
describes an immutable artifact and cannot go stale."* **That reasoning is correct and this record
does not dispute it.** A digest names exact bytes; given digest D, contents are fixed forever.

Two gaps sit underneath it, and both are ours rather than the image owner's.

### Gap 1 — the digest is recorded but never verified

`0006` selects a `provides` document by matching the lane's `runs-on` **labels** against
`identity`. Labels, not digests. So `identity.image_digest` is written into the document and **nothing
confirms at check time that the container actually running is that digest.**

A `provides` claiming `valid_for: immutable` on the strength of a digest, matched by label, and never
re-verified, is a permanent green resting on an unchecked premise. **Ask R6's question of it: if this
lost its ability to see, what would go red?** Nothing. The document would keep asserting an old
image's capabilities against a new image indefinitely, and every `hutch check` would pass.

### Gap 2 — source stability does not imply content stability

With an unversioned install in the build, **rebuilding an unchanged Dockerfile produces a different
image with different contents and a different digest.** So "the Dockerfile did not change" is not
evidence that a `provides` document is still current — and that is precisely the inference a
maintainer will make when deciding whether re-measuring is necessary.

## Proposed decision

**1. `hutch check` verifies identity, and cannot verify it silently.**
Where the running host exposes its image digest, the checker compares it to `identity.image_digest`;
a mismatch is `FAIL` and **an unreadable digest is `SKIP`, not an assumption** (R3). If a digest
cannot be read from inside the container on the fleet's runtime, that is itself the finding, and the
honest outcome is `SKIP` rather than a green built on hope.

**2. `provides` records how its identity was established, not just what it is.**

```yaml
identity:
  id: sasha-ubuntu
  image_digest: sha256:abc123...
  declared:                          # NEW
    base: digest                     # digest | tag | none
    packages: unversioned            # pinned | unversioned | mixed
```

`declared.packages: unversioned` is legal and honest. What it forbids is `valid_for: immutable`
combined with an undeclared package layer — because the pair is a contradiction, and stating both
lets the checker say so instead of a human noticing twenty-eight lines apart.

**3. Re-measure per image BUILD, never per Dockerfile change.** A build is the event that can change
contents; a source edit is not. This follows from gap 2 and costs nothing, since the build is already
running.

## Consequences

- Item 1 is the load-bearing one and it may be blocked by the runtime: rootless podman may not expose
  the digest to the container. **If so, the answer is `SKIP` plus a recorded finding — not a
  relaxation.** That outcome would be worth knowing on its own, since it would mean digest-based
  `immutable` is unverifiable on this fleet and `0005` should stop offering it there.
- Item 2 adds three lines to a generated document and costs the probe nothing: both facts are
  readable from the image build that already produces it.
- Item 3 is a scheduling change, not a code change.

## Note on scope

This is filed as a record rather than sent as a message, per [`0001`](0001-decisions-live-in-this-repo.md).
It needs no reply — amend it, accept it, or reject it in the repo. If item 1 turns out to be
impossible on the fleet's runtime, rejecting it *with that measurement recorded* is a better outcome
than accepting it.
