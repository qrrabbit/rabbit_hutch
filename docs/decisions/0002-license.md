# 0002 — License: AGPL-3.0

- **Status:** accepted
- **Date:** 2026-08-09
- **Supersedes:** the Apache-2.0 recommendation in this record's first draft, which was wrong for the
  stated requirement.

## Context

The requirement from the copyright holder, in his words: *"whatever suggestion you have is good, I
don't particularly care, just don't let others take advantage of me. Mine is mine — can use it if
they like, but not make money on me."*

**The first draft of this record recommended Apache-2.0. That was a mistake and is corrected here.**
Apache-2.0 and MIT both explicitly permit commercial use, including taking the work proprietary and
selling a product built on it. They protect the *user*, not the author. Recommending one against this
requirement got the trade exactly backwards.

## Decision

**AGPL-3.0.** `LICENSE` holds the verbatim canonical text from
`https://www.gnu.org/licenses/agpl-3.0.txt` (34,523 bytes), unmodified.

Copyright is retained by the author; see `README.md`.

## Why this one

The requirement has two halves and AGPL-3.0 is the standard way to express both.

- **"Can use it if they like."** Anyone may run, study, modify and distribute it, including inside a
  company. Nothing here restricts use.
- **"Not make money on me."** Any distributed or **network-hosted** modified version must publish its
  source under the same terms. Nobody can take this, close it, and sell it as their own product.

The network clause is the load-bearing part for this project specifically: a CI checker is exactly
the kind of thing someone would wrap into a hosted service. Plain GPL-3.0 would leave that open —
hosting is not distribution — so AGPL is the correct member of the family here, not merely the
strictest one available.

## Consequences, stated honestly

- **AGPL does not forbid anyone from making money.** It forbids *proprietary* use. A company may
  charge for support or hosting provided they publish their modifications. If the requirement were
  literally "no one profits, ever", that needs a non-commercial licence — which is **not** an
  open-source licence, is notoriously vague about what counts as commercial, and would prevent a
  company from using this in its own CI at all. That would defeat the purpose of publishing it. AGPL
  is the closest well-understood licence to the stated intent, and it is a deliberate choice rather
  than a perfect match.
- **AGPL narrows corporate adoption.** Some organisations prohibit AGPL dependencies outright. This is
  a real cost and it is accepted knowingly: protecting the author's position was named as the
  priority, adoption breadth was not.
- **The author retains copyright and may dual-license.** He can offer a separate commercial licence to
  anyone who wants terms other than AGPL. That is the standard posture for "mine is mine, use it
  freely, pay me if you want it on other terms", and it keeps the commercial door open without
  deciding anything now — which matters, since how far this goes is explicitly undecided.
- ⚠️ **That dual-licensing option survives only while one party holds all the copyright.** See
  [`0003-contributor-agreement.md`](0003-contributor-agreement.md). It must be settled *before*
  outside contributions are accepted, not after.
