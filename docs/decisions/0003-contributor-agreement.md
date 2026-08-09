# 0003 — Contributor agreement, before outside contributions

- **Status:** PROPOSED — open, owner's decision. **Must be settled before the first outside
  contribution is merged, not after.**
- **Date:** 2026-08-09

## Context

[`0002`](0002-license.md) chose AGPL-3.0 and noted that the author keeps the option to **dual-license**
— to sell a commercial licence to anyone who does not want AGPL terms. That is what makes "mine is
mine, use it freely, pay me if you want other terms" enforceable, and it is worth real money if this
ever goes anywhere.

**That option is destroyed silently by the first merged contribution from someone else.**

A contributor owns the copyright in their patch. Once third-party code is in the tree under AGPL, the
project can only be relicensed with **every** contributor's agreement — which in practice means never,
because people move on and become unreachable. Nothing warns you when this happens; a five-line pull
request is enough.

This is currently unresolved and the risk is not yet live: contributions so far come from projects
under the same ownership. It becomes live the moment the repository is promoted anywhere — which is
deliberately deferred, so there is time to decide properly.

## Options

**(a) DCO — Developer Certificate of Origin.** Contributors add `Signed-off-by:` to commits,
certifying they wrote it and may submit it. Near-zero friction, widely used, well understood by
contributors. **It does not transfer or license copyright to the owner, so it does NOT preserve
dual-licensing.** It solves provenance, not relicensing.

**(b) CLA — Contributor License Agreement.** Contributors grant the owner a licence broad enough to
relicense (or assign copyright outright). **Preserves dual-licensing.** Costs friction: a signing step,
a bot, and some contributors — particularly employed engineers whose employers must approve — will
simply not bother. A known deterrent to casual contribution.

**(c) Nothing.** Accept AGPL-only forever. Simplest, most contributor-friendly, and forecloses
commercial licensing permanently.

## Recommendation

**(b), a lightweight CLA, but only when it is actually needed — i.e. immediately before the repository
is promoted publicly.** Adding it now would impose friction with no benefit, since present
contributions are same-owner. Adding it after outside contributions have landed is too late.

If the commercial option is judged worthless, **(c) is entirely respectable** and cheaper than (b) —
but that should be a decision, not something arrived at by not noticing. This record exists so it is
the former.

⚠️ **Trigger:** revisit this the moment promotion is contemplated, and before merging any pull request
from outside the owner's own projects. That trigger is the whole reason this is written down.

## Consequences

- Whichever is chosen, record it here and state it in `CONTRIBUTING.md` so contributors are not
  surprised.
- (b) means a bot and a signing step; budget an hour.
- (c) is irreversible in practice. That is the point of writing it down before it happens by accident.
