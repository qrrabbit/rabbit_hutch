# Q — should the fleet image carry `postgresql-client-16` rather than 12?

- **Status:** OPEN — for the fleet image owner
- **Raised:** 2026-08-09, by the OutBaking architect
- **Timely:** the image is being rebuilt anyway to bake Chromium's shared libraries. Answering this
  now costs one image build; answering it later costs a second one.

## The measurement

Fleet replica `vgr-sasha-ubuntu--baking-scheduler-02`, run `31270809170`:

```
| ✅ | psql client | psql (PostgreSQL) 12.22 (Ubuntu 12.22-0ubuntu0.20.04.4) at /usr/bin/psql |
| ✅ | Postgres server on host.containers.internal:5432 | pg_isready accepted the connection |
```

**A v12 client against a v16 server.** That is skew the GitHub-hosted runner never had, because its
image shipped a client matching the server the lane started.

## Why it is a question and not a requirement

`go-page-gate` applies OutBaking's whole migration set through `psql`, including `psql -v` variables
and `\if` meta-commands. A v12 client will very likely run all of it — libpq is famously tolerant
across versions, and `\if` has existed since v10.

**But we have not tested it, so we are not asserting it.** Declaring `min_version: "16"` in our
`requires` would be precisely the unverified confident claim this repository exists to prevent (R1),
and it would also cost you a package you may not need. Declaring nothing and discovering a
meta-command incompatibility during a migration would be the other failure.

So it is recorded as an open question against the artifact, not smuggled in as a constraint.

## What would settle it

Either is fine and both are cheap:

1. **The fleet answers it by policy** — if `postgresql-client-16` is a one-line addition to an image
   already being rebuilt, add it and the question closes with no testing. Matching client to server
   is good practice independent of our lane, and it removes a whole class of future surprise for any
   other adopter whose migrations use meta-commands.
2. **OutBaking answers it by measurement** — run the tracked migration set through a v12 client
   against a v16 server and report. This is ours to do and does not block you; it just takes longer
   than option 1 and only answers it for our migration set rather than in general.

**Recommendation: option 1**, purely on timing. The marginal cost inside an image build that is
already happening is close to zero, and it converts an open question into a settled fact for every
future consumer rather than only for us.

## Note on the ask

This is a *question*, not a blocker. `go-page-gate` cannot move for the Chromium libraries and the
CI-owned Postgres instance regardless; if the answer to this is "no, 12 is what the base image
gives", we will measure it on our side and set `min_version` accordingly — or not, if it works.
