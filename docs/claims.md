# What a platform may claim

> **Status: draft proposal.** This is a draft proposal for discussion and review by the DTGWG (Trust over IP Decentralized Trust Graph Working Group). It is not an adopted or endorsed standard or test suite.

A result of this suite is a measurement of one surface of one platform, at one commit, by one suite version. This page says how to state such a result, so that a summary, a README badge or a slide says no more than the run shows. It applies to the suite's own pages and is offered to platforms that cite the suite.

Back to [the docs index](README.md). Terms are defined in the [glossary](03-glossary.md); how results are read is in [Reading results](reading-results.md).

## 1. The claim template

A claim names everything a reader needs to find and repeat the run:

```
<platform> <commit or release>, surface <surface>, measured with dtgwg-conformance <suite version> (<suite commit>),
run <run folder>, Tier <n>, channel registry (draft specifications): reach <ran> of <total> vectors;
PASS <p>, FAIL <f>, KNOWN-FAIL <k> (<vector ids>), CLARIFY <c>, SKIP <s> (reasons in the run folder).
```

- **Suite version and commit** come from the run's `run.json` (`suite.version`, `suite.commit`, `suite.dirty`; [docs/04 section 20.1](04-suite-design.md#201-every-run-folder-carries-runjson)). A run with `suite.dirty: true` is not cited.
- **Run folder** is the folder under `<results dir>/runs/` that holds the results; a claim without one cannot be checked.
- **Vector ids** are cited as `<suite version>/<id>` (for example `0.1.0-draft/BND-002`; [docs/04 section 20.3](04-suite-design.md#203-vector-ids-never-reused-retired-rather-than-deleted-cited-with-the-version)). Every KNOWN-FAIL is listed by id, not only counted.
- **Counts** are copied from `results.json`, never rounded or merged across surfaces or platforms. Advisory channels (candidate, proposed) and CLARIFY are reported separately and never added to a gating figure.
- **Reach** is always stated: a high PASS count over a small reach says little.

Example, with the shape only (the figures are placeholders, not a result):

```
Platform X a1b2c3d, surface HTTPS server, measured with dtgwg-conformance 0.1.0-draft (ce5e1ad),
run <results dir>/runs/2026-11-15-platform-x-a1b2c3d, Tier 1, channel registry (draft specifications): reach 120 of 300 vectors;
PASS 100, FAIL 2, KNOWN-FAIL 3 (0.1.0-draft/BND-002, 0.1.0-draft/ERR-006, 0.1.0-draft/PRF-101), CLARIFY 15, SKIP 180 (reasons in the run folder).
```

## 2. Words that are not used

<!-- lint:skip -->
The words "conformant", "compliant" and "certified", and their forms (non-conformant, nonconformant, conformantly, non-compliant, compliance, certify, certifies, certification), are not used about a platform or a result
<!-- /lint:skip -->
unless both of these hold:

1. the run used a **released** suite version: a semver version without a pre-release part (no `-draft`, no `-rc.1`; [docs/04 section 20.2](04-suite-design.md#202-suite-versions-semver)), and
2. the run has **FAIL 0 and KNOWN-FAIL 0** on the surface the claim names.

No released suite version exists: the current version is `0.1.0-draft`, a pre-release. So today no platform meets the rule, and the words are not used anywhere in this repository. Even when both conditions hold, write "passes every gating vector of dtgwg-conformance <version> on <surface>", not a status word: the suite is a draft proposal, it awards no status, and nobody has adopted it.

Use instead: "passes", "fails", "measured", "differs from the specification text at", "follows the specification on", "the refusal the framework states". The interoperability wording rules ([docs/14](14-interoperability-tracking.md), [decisions.md, interoperability wording](decisions/04-ceremonies-governance-and-exceptions.md#interoperability-wording)) still apply on top.

## 3. The lint

`scripts/lint-claims.ts` (`npm run lint:claims`) and `test/claims-lint.test.ts` scan `README.md`, `STATUS.md`, `CONTRIBUTING.md`, `CHANGELOG.md`, `platforms.md`, every Markdown page under `results/` (the generated pages and the run READMEs too) and everything under `docs/` for the words above, whole words in any case, and fail on any use. `npm test` and `npm run check:docs` both run it. A page may quote the words, as section 2 does, only inside a `lint:skip` block:

```
<!-- lint:skip -->
quoted text
<!-- /lint:skip -->
```

This is the same convention as the governance-probe lint ([docs/17 section 2](17-governance-probes.md)). A skip block is for naming or quoting the words, never for making a claim with them.
