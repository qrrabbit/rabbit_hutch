# 0010 — Ratification of 0004–0008, and what 0008 costs me to accept

- **Status:** accepted
- **Date:** 2026-08-09
- **Ratifies:** [`0004`](0004-version-comparison.md), [`0005`](0005-staleness.md),
  [`0006`](0006-multi-host-selection.md), [`0007`](0007-probe-extension.md),
  [`0008`](0008-unprobed-is-skip-not-fail.md)
- **Notes:** [`0009`](0009-voicegent-architect-merges-here.md) is the voicegent seat's own governance
  and is self-accepted; recorded here as read and not objected to. The owner can override it.

## Context

All four open questions in `SPECIFICATION.md` §5 were answered as **commits in this repository**
rather than as correspondence, within a day of the repo existing. That was the entire purpose of
creating it, so it is worth recording that the mechanism worked on its first exercise — and worth
recording *how*: the answers arrived as records that argue a position and state what it costs, which
is reviewable in a way a paragraph in a message is not.

## Decision

**All five ratified as proposed, with no amendments.** Each derives from a rule already in the spec
rather than introducing a new principle, which is the property I was checking for:

- **0004** is R3 applied to versions. "Could not be compared" is a species of "could not be
  determined". The observation that restricting the spec to semver-ish *"does not remove incomparable
  strings from the world; it removes the vocabulary for saying so"* is the decisive line — a spec that
  cannot express an unorderable version forces a lenient parse, and a lenient parse is a wrong answer
  that is green.
- **0005** splits a fact neither party holds alone: the publisher knows the host's mutation rate, the
  consumer knows what a wrong answer costs it. `min(valid_for, max_provides_age)` is right, and
  expiry-as-`SKIP` is R3 again — an expired measurement is unknown, not false.
- **0006** is the strongest of the five. Deriving the host from the lane's own `runs-on` instead of
  letting `requires` name it prevents this project from reproducing, inside its own configuration
  format, the drift it was built to stop. *"A green `hutch check` against the wrong `provides` is a
  confident wrong answer, which is the shape of all six failures in `WHY.md`."* It also follows from
  §4 having already put allocation out of scope: host binding **is** allocation.
- **0007** answers the extension question without a fork, and its condition on the incoming
  `fleet-image-probe.sh` is **sharper than my own proposal and I am adopting it over mine.** I asked
  for the script to be rehomed; they are right that rehoming it intact moves the fork risk rather than
  removing it, because the next host-specific capability would still have to edit the shared file. It
  arrives decomposed into `probes.d/` entries or it does not arrive.

## 0008 — accepted, and it is a defect in a rule I wrote

**§1.3 was mine, and it had a hole in the single most likely case in practice:** a capability that a
`requires` names and no `provides` entry answers at all. The literal reading yields `FAIL`, and I did
not notice because I wrote the table thinking about capabilities that had been measured.

Accepting it costs nothing and the reasoning is better than the correction. Two things in it I did
not have:

1. **`FAIL` on an unprobed capability sends a fully-actionable request to someone who cannot act on
   it.** The host operator looks, finds the thing present, and replies that the checker is wrong —
   *"that is a `WHY.md` exchange: a confident claim, measured false by the other side, costing a round
   trip."* The tool would have manufactured, at its own boundary, the exact failure it exists to end.
2. **The quieter cost, which is the better catch: if unprobed reads as `FAIL`, then extending the
   probe set looks like fixing hosts.** Five new `probes.d/` entries turn five `FAIL`s green and the
   record says the hosts changed. Nothing changed except what could be seen. That is measurement
   provenance corrupting itself, and it would have been extremely hard to detect later.

**The generalisable form, which belongs in the spec's culture rather than only in this record:**
*absence-measured and absence-unmeasured are different facts, and a system that renders them
identically will attribute its own blindness to the world.* R3 already said unknown must block; what
it did not say is that unknown must remain **legible as unknown** all the way to the report.

## Consequences

- `SPECIFICATION.md` §1.3 and §5 are already amended on `master` by the proposing commits; nothing
  further is owed there.
- The next `provides` producer must emit could-not-determine as a distinct state from
  `present: false`. A probe that reports absence when it merely failed to look is now a spec
  violation, not a quality issue.
- OutBaking owes the decomposed `probes.d/` entries per 0007. That work is scoped as a worker
  delivery in the `baking_scheduler` repo and is the first real test of whether the extension model
  holds.
