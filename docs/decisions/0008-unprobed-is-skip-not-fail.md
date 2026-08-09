# 0008 — A required capability that no probe answers is SKIP, not FAIL — R3 has a hole

- **Status:** proposed
- **Date:** 2026-08-09
- **Amends:** SPECIFICATION.md §1.3
- **Proposed by:** the voicegent architect
- **Found while:** answering §5 question 4, not while looking for a defect

## Context

§1.3 defines three outcomes:

| outcome | meaning |
|---|---|
| `PASS` | required, present, and any version constraint satisfied |
| `FAIL` | **required and absent**, or present at an unsatisfying version |
| `SKIP` | **could not be determined** |

R3 then says UNKNOWN blocks and never defaults to PASS. Both are right. **But the table does not say
which outcome applies when a capability appears in a `requires` and does not appear in the `provides`
document at all** — and that is the single most likely state in practice, because it is what happens
the first time any consumer declares a need the shared probe has never answered.

An implementer reading §1.3 literally will map "not in provides" to *absent* and emit `FAIL`. That is
the safe-looking reading and it is wrong.

## The distinction, and why it is not pedantry

`FAIL` and `SKIP` differ in **who owns the next action** — which is the whole content of R5.

- **`FAIL`** means *the host does not supply this.* Owner: whoever operates the host or builds the
  image. Action: install it, bake it, or the lane cannot run here.
- **`SKIP`** means *nobody measured this.* Owner: whoever maintains the probe set. Action: add a
  `probes.d/` entry.

Reporting `FAIL` for an unprobed capability sends a correct-looking, fully-actionable request to a
person who cannot act on it — the host operator will look, find the capability present, and reply
that the checker is wrong. **That is a `WHY.md` exchange: a confident claim, measured false by the
other side, costing a round trip.** This tool exists to end those, so it must not manufacture one at
its own boundary.

There is a second cost, quieter and worse. If unprobed reads as `FAIL`, then **extending the probe set
looks like fixing hosts.** A team that adds five `probes.d/` entries and watches five `FAIL`s turn
green will reasonably conclude the hosts changed. Nothing changed except what could be seen, and the
record will say otherwise.

## Decision

**Amend §1.3 to distinguish absence-measured from absence-unmeasured, and make the outcome table
state the owner.**

| outcome | meaning | exit |
|---|---|---|
| `PASS` | required, present, any version constraint satisfied | 0 |
| `FAIL` | required, and a probe **measured it absent** (`present: false`), or present at an unsatisfying version | non-zero |
| `SKIP` | **could not be determined** — the probe returned could-not-determine, **or no probe answered this id at all**, or a version comparison was not possible ([0004](0004-version-comparison.md)), or the `provides` is expired ([0005](0005-staleness.md)), or host selection was ambiguous ([0006](0006-multi-host-selection.md)) | non-zero |

Both still block, so **no lane becomes less safe.** What changes is only who is told to act — and
that is the entire value of the report.

`present: false` therefore becomes load-bearing: it is a **positive measurement of absence**, not a
default. A probe that cannot determine presence must emit could-not-determine (exit 2), never
`present: false`. Worth stating in §1.1, since the natural implementation writes `false` on any
non-zero exit and thereby converts every unmeasurable capability into a false accusation against the
host.

## Why this belongs in the spec rather than in an implementation

Every failure in `WHY.md` is a **capability fact asserted by one side and measured false by another.**
`present: false` written by a probe that did not measure is precisely such an assertion, emitted by
the tool built to prevent them. The spec should close it before an implementation makes the choice by
accident.

## Consequences

- The `SKIP` bucket becomes the common outcome early in adoption, which is correct and should be said
  out loud in the README so nobody reads an all-SKIP first run as the tool being broken.
- R6's positive control gains an obvious member: **a `requires` naming a capability id no probe
  answers must SKIP.** A checker that has grown a permissive fallback fails its own control.
- `hutch check` output should group by owner rather than by lane, so the host operator and the probe
  maintainer each see their own list. That follows from R5 and is worth a line in §2.
