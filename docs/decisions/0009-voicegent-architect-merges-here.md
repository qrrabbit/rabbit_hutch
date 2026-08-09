# 0009 — The voicegent architect maintains this repo and merges its own branches here

- **Status:** accepted
- **Date:** 2026-08-09
- **Decided by:** the owner, delegating

## Context

The owner delegated maintenance of this repo to the voicegent architect seat: *"you merge now… go
ahead and manage it."*

This needs recording because **it inverts a rule that seat holds as non-negotiable in its own repo.**
`session_prompts/ARCHITECT.md` in `voicegent.ai` states that the architect never pushes to master —
the operator merges, always. A successor architect arriving here with that rule in memory and no
record of this delegation will freeze rather than merge, and will ask the owner to do something he
has explicitly handed over. That failure has a precedent in the other repo (an architect kept a
harness-assigned branch name for a full session because a general rule appeared to forbid renaming
it, when the rename was sanctioned).

## Decision

**In `rabbit_hutch`, the voicegent architect authors on a branch and merges it to `master` itself.**
In `voicegent.ai`, the operator still merges. The two rules coexist; neither is a relaxation of the
other.

The branch-then-merge shape is kept rather than committing straight to `master`, because the diff is
the review even when the author and the merger are the same seat.

## Scope, and the boundary that matters

Delegated: merges, decision records, spec amendments, answering open questions, probe entries.

**Not delegated, and this is the point of the repo:** anything architectural *between* voicegent and
another project still routes through the owner. This repo exists because eight exchanges between two
architects turned out to be capability FACTS rather than design disagreements — facts a probe answers
in thirty seconds. It is a channel for facts. Giving it a channel for design would rebuild the thing
it replaced, one layer out.

The honest cost, stated so it can be noticed if it happens: the split was made to keep CI work out of
the voicegent architect's context. Maintaining this repo spends a little of that context back. Merges
and records are cheap; if this repo starts generating design debate, the split has failed and that is
the signal to say so rather than absorb it.
