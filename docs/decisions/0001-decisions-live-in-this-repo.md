# 0001 — Decisions live in this repository

- **Status:** accepted
- **Date:** 2026-08-09

## Context

This project was extracted from a four-day, eight-round correspondence between two engineering teams
about what a shared CI host provided. Every round transferred a capability fact; none was a design
disagreement. The facts existed — they were simply not written down anywhere both parties could read.

One of the two teams hit the same shape independently in their own documentation: an adoption guide
that existed only inside a chat relay, so the other team could be told to read a file nobody could
find. Their words on fixing it: *"it is now committed rather than living in a chat relay — which is
exactly why you could read an ADOPTING.md that nobody could find. That was our defect."*

## Decision

Every decision that changes the contract is recorded here, in this directory, in the repository.

A decision record is a numbered Markdown file: context, the decision, the consequences, and — where a
decision rests on a measurement — the command that produced it and the host it ran on.

Correspondence may *precede* a decision. It may never *be* the decision.

## Consequences

- A contributor arriving cold can reconstruct why the contract has its current shape without asking
  anyone. That property is the point; it is what a chat thread structurally cannot provide.
- Decisions get slower to record and much faster to look up. That trade is correct for a contract
  intended to outlive the conversation that produced it.
- Records are append-only in spirit: a superseded decision is marked superseded and kept, never
  deleted. The reasoning that was wrong is often more useful later than the conclusion that was
  right — see [`WHY.md`](../../WHY.md), which is entirely a record of confident claims that turned
  out to be false.

## Format

```
# NNNN — short imperative title

- Status: proposed | accepted | superseded by NNNN
- Date: YYYY-MM-DD

## Context      — what forced the decision
## Decision     — what was decided, in the active voice
## Consequences — what this costs, not only what it buys
```
