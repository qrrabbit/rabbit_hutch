# 0004 — Version comparison: the probe declares the scheme, and an incomparable pair SKIPs

- **Status:** proposed
- **Date:** 2026-08-09
- **Resolves:** SPECIFICATION.md §5 question 1
- **Proposed by:** the voicegent architect (same owner; see CONTRIBUTING.md)

## Context

`min_version: "16"` against a measured `"12.22"` is unambiguous. `min_version: "3.4"` against
whisper.cpp's `"1.7.4-31-gf1b2c3d"`, Debian's `"2:12.22-0ubuntu0.20.04.1"`, or ffmpeg's
`"n6.1.1-3-g9a2f7c"` is not. Two options were on the table: restrict to semver-ish, or allow a
declared comparator per capability.

## Decision

**Neither restrict nor default. The `provides` entry declares the scheme its measurement is in, and
a comparison the checker cannot perform is a `SKIP`.**

```yaml
capabilities:
  - id: pkg.postgresql-client
    present: true
    version: "12.22"
    version_scheme: semver          # semver | pep440 | debian | opaque
    how: "psql --version"
```

- The **probe** sets `version_scheme`, because the probe is the only party that knows which tool
  produced the string.
- `version_scheme: opaque` is legal and expected — it means *this version string is not orderable*.
- A `requires` carrying `min_version` against an `opaque` capability is **`SKIP`, and `SKIP` blocks**
  (R3). It is not a `FAIL` and it is emphatically not a lenient parse.
- No `min_version` and no comparison happens: presence is the whole question, which is the common case.

## Why this one

**This is R3 applied to versions, and the alternatives both violate it.** "Could not be compared" is
a species of "could not be determined". Restricting the spec to semver-ish does not remove
incomparable strings from the world; it removes the *vocabulary* for saying so, which forces every
real vendor string into a lenient parse. A lenient parse of `2:12.22-0ubuntu0.20.04.1` will return an
answer, that answer will be silently wrong, and it will be **green** — the exact
green-while-blind mode R6 exists to convert into red.

**The scheme is derived, not enumerated.** The alternative — a comparator table in the checker,
keyed by capability id — is a hardcoded list that must be edited every time a new capability appears,
and it is maintained by someone who is not looking at the tool that emits the string. Putting the
scheme on the measurement puts it with the only party holding the fact.

**`opaque` must be ordinary, not an escape hatch.** If declaring it feels like an admission, probe
authors will guess `semver` to keep lanes green, and the rule inverts into the failure it prevents.
The spec should say plainly: most version strings in the wild are opaque, and saying so is the
correct answer, not a gap.

## Consequences

- A consumer wanting a version floor on an opaque capability must either get the probe to emit a
  comparable field (often possible — `pg_config --version-num` yields `120022`), or express the need
  as presence plus a separate capability id. Both are better than a wrong comparison.
- `hutch check`'s positive control (R6) should include an **incomparable pair that must SKIP**. A
  checker that has quietly grown a permissive fallback then fails its own control instead of passing
  a lane it cannot actually verify.
