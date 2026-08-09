# Contributing

## The one rule that matters

**Decisions land in this repository, not in correspondence.**

This project exists because capability facts lived in conversation instead of in a file. It would be
a poor joke to design it the same way. So:

- A decision that changes the contract becomes a record in [`docs/decisions/`](docs/decisions/).
- A capability claim in an issue or a PR body should be accompanied by the command that produced it
  and the host it ran on — or marked explicitly as a guess.
- "We discussed this and agreed" is not a citation. If it is not in the repo, it did not happen.

The same applies to relaying a finding from elsewhere: **a cross-project finding is a citation, not a
paraphrase.** Quote the measurement. Bending someone else's evidence toward your own point is easy to
do by accident and expensive to unwind.

## Branch

`master`. Not `main` — a deliberate preference, not an oversight.

## What good contributions look like

**Best:** a capability id that your host exposes and the spec cannot currently express, with the
probe command that measures it and an example of the `provides` entry it should produce.

**Also valuable:**
- A failure this contract would *not* have caught. Those are the most useful bug reports here — the
  spec's value is bounded by the failure modes it can express.
- A second consumer. The specification is explicitly unstable until two independent projects have run
  against it; being the third is worth more than any amount of review.
- Corrections to [`WHY.md`](WHY.md) if you can show a claim there is wrong. It would be fitting.

**Please don't:**
- Add capability ids speculatively. An id nobody measures is an id that will be wrong when someone
  finally does. The section on this in the spec (R1) is not decorative.
- Propose auto-installation of missing dependencies. That is a non-goal and the reasoning is in the
  README — quietly installing a missing dependency is how environment drift becomes invisible.

## Proposing a change to the contract

1. Open an issue describing the failure it would have caught. Start from the failure, not the design.
2. If it changes `SPECIFICATION.md`, add a decision record in the same PR — see
   [`docs/decisions/0001-decisions-live-in-this-repo.md`](docs/decisions/0001-decisions-live-in-this-repo.md)
   for the format.
3. Version bumps to `hutch: N` require two consumers to agree. Until 1.0 the contract may break; after
   1.0 it may not, and that is the whole reason 1.0 is being held back until real use has tested it.

## Status of this document

Early. If a process rule here is getting in the way of a good contribution, say so — the rule is
probably wrong.
