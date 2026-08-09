# Why hutch exists

Two independent projects share a self-hosted CI runner fleet. Over four days, moving one CI job onto
that fleet took eight rounds of correspondence between the two teams.

Afterwards we went back and classified every round. **Not one was a design disagreement. Every single
one was a capability fact that one side asserted and the other measured false.**

That is the entire case for this tool. Nobody was careless; the parties involved were the people who
built the systems in question. The mechanism was wrong: capability facts were *unpublished*, so they
were discovered by conversation.

---

## The claims

| # | claim, asserted confidently | measured |
|---|---|---|
| 1 | "Steps run as an unprivileged in-image user, so `sudo apt-get` fails." | **False.** `sudo -n true` succeeded; the runner user was in the `sudo` group. It arrived with the pinned base image and was never noticed. |
| 2 | "The image installs nothing beyond the runner agent — no Postgres client." | **False.** `psql (PostgreSQL) 12.22` was already baked in at `/usr/bin/psql`. |
| 3 | "Revoke `CONNECT` on `template1` and every database created afterwards inherits the revocation." | **False.** Database ACLs live in `pg_database.datacl` — catalog metadata *about* the database, not contents *inside* it. `CREATE DATABASE` copies contents and assigns the default ACL. Extensions travel through `template1`; ACLs do not. PostgreSQL has no default-privileges mechanism for databases at all. |
| 4 | "A CI role of `LOGIN CREATEDB`, never superuser, is sufficient." | **False for that migration set.** One migration runs `ALTER FUNCTION … OWNER TO <role>`; PostgreSQL requires the new owner to hold `CREATE` on the schema, and only a superuser bypasses that check. The set reached that migration and died. |
| 5 | "Bootstrap the test database with `GRANT CREATE ON SCHEMA public`." | **Wrong schema.** The objects were in a named schema, not `public` — and that schema is *created by* the migration set, so there was nothing to grant on at bootstrap time. |
| 6 | "Namespace each project's roles by prefix." | **Not possible without editing the artifact under test.** The role names were fixed literals across 115 references in the migrations. Renaming them changes what production runs, so CI would stop proving what ships. |

**One claim survived measurement:** there is genuinely no docker socket, deliberately, because the
runner's unprivilege is meant to be the security boundary. That one was true, load-bearing, and
correctly documented.

---

## What the pattern tells you

**1. The false claims were not random — they were all *inherited*.** Each was true when someone first
observed it, or plausible by analogy, and then travelled onward as a fact with no date, no owner, and
no re-test trigger. Claim 1 was documented in a Dockerfile as the *justification* for baking every
dependency — so when it turned out to be false, nothing was enforcing the discipline it justified.

**2. Analogy is not measurement.** Claim 3 is the cleanest example: revoking on a template *feels*
like it should inherit, because extensions genuinely do work that way. One query settled it. The
party who tested it was right; the party who reasoned about it was wrong.

**3. The fix is not "be more careful."** Every party here was careful. A capability claim needs to be
*executable* — produced by a probe, stamped with when and where, and checked automatically — because
prose claims decay silently and no amount of diligence catches that.

**4. Discovering a privilege is not the same as being granted it.** Claim 1 revealed that jobs *could*
escalate. The correct response was not to start using it — it was to report it, because the host
operator's stated model said otherwise, and the likely fix was to remove it. Building on it would
have converted their repair into our outage.

---

## The cost

Roughly eight exchanges, four days of latency, and a meaningful share of two engineers' working
context — to transfer facts that a probe produces in under thirty seconds.

The probe was eventually written. It ended three of these disputes in a single run, and its output
table is the direct ancestor of the `provides` document in
[`SPECIFICATION.md`](SPECIFICATION.md).

`hutch` is that probe's output, made into a contract.
