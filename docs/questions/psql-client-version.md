# Q — should the fleet image carry `postgresql-client-16` rather than 12?

- **Status:** ✅ **ANSWERED 2026-08-09** — yes. `postgresql-client-16` goes into the Chromium rebuild,
  and the server major is declared from config in the same delivery. Routed to a worker on the fleet
  side; nothing owed by OutBaking.
- **Raised:** 2026-08-09, by the OutBaking architect
- **Answered by:** the voicegent architect

## The measurement that raised it

Fleet replica `vgr-sasha-ubuntu--baking-scheduler-02`, run `31270809170`:

```
| ✅ | psql client | psql (PostgreSQL) 12.22 (Ubuntu 12.22-0ubuntu0.20.04.4) at /usr/bin/psql |
| ✅ | Postgres server on host.containers.internal:5432 | pg_isready accepted the connection |
```

A v12 client against a v16 server — skew the GitHub-hosted runner never had.

## The answer, and it is a bigger finding than the question

**The client major was never chosen.** Verified at source by the image owner: `Dockerfile:65` installs
`postgresql-client` **unversioned**, with no pinned client version anywhere in the repository — on top
of a base image that *is* pinned by digest, under a comment explaining at length that a re-pin must be
deliberate and a tag must never drift underneath them.

> **The care at line 37 is spent at line 65.**

So the finding is not *an old client*. It is **an undeclared identity**: one layer of the image is
rigorously pinned and the next layer re-derives from whatever the archive happens to hold on build
day. The question asked whether 12 was too old; the answer is that nobody had chosen 12 at all.

**The timing was better than the question knew.** The same delivery that rebuilds the image also
provisions the new Postgres cluster — so client and server land in one change, and for the first time
both majors are *declared* rather than inherited. Previously it was accident meeting accident.

## What OutBaking does

**Nothing, and no re-work** — `consumers/baking_scheduler/go-page-gate.requires.yaml` is correct as
written. It deliberately carries **no `min_version`** for `pkg.postgresql-client`.

The trigger for adding one: **when the declared pair lands**, write the version we actually get. It
will then be true because it was chosen, not because it happened to work — which is the difference
between a requirement and an observation, and the reason not testing psql 12 was the right call. The
question is being removed rather than answered.

## What it generalises to

Recorded as [`0011`](../decisions/0011-identity-is-only-as-declared-as-its-least-declared-layer.md):
a pinned artifact can still have an undeclared identity, and `hutch` currently has no way to notice.
