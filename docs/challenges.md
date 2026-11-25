# Challenging a vector

> **Status: draft proposal.** This is a draft proposal for discussion and review by the DTGWG (Trust over IP Decentralized Trust Graph Working Group). It is not an adopted or endorsed standard or test suite.

A vector is the suite maintainer's reading of a specification. An implementer who thinks a vector reads the text wrongly can challenge it. This page is the process: who may challenge, on what grounds, with what evidence, what happens to results while a challenge is open, and how it ends. It follows the challenge route of a TCK (technology compatibility kit): the people measured by a vector have a written way to dispute it, and every outcome is recorded with its reason.

Back to [the docs index](README.md). Terms are defined in the [glossary](03-glossary.md).

## 1. Where to raise a challenge

Raise a concrete vector challenge in the repository's GitHub issue tracker, using the vector-bug template and the evidence described in section 4. The maintainer records the challenge and its disposition in the challenge file. Questions that need discussion can start in GitHub Discussions, as described in [CONTRIBUTING](../CONTRIBUTING.md). This does not authorize filing issues against measured platforms or sending questions to the task force on the project owner's behalf.

## 2. Who may challenge

Anyone may: an implementer whose platform is measured, a specification editor, or the maintainer re-reading a vector. A challenge from the platform the vector measures carries no extra weight and no less: it is decided on the text.

## 3. Grounds

A challenge names one ground. These are the three CLARIFY classes, so an upheld challenge maps straight onto one:

| Ground | Meaning | Typical evidence |
|---|---|---|
| `spec-silent` | The specification text does not decide the behaviour the vector asserts; more than one answer is acceptable. | The cited section, quoted, and the sentence that is missing. |
| `spec-inconsistent` | Two pieces of specification text (two documents, or a document and its schema or fixtures) say different things, and the vector picks one. | Both passages, quoted, with file and line at the pinned commit. |
| `vector-overreads` | The text is clear, and the vector expects more than it says: it turns a SHOULD or MAY into a MUST, asserts a design rule of the suite as if it were specification text, or checks a detail the text leaves to the implementation. | The cited text, quoted, and the exact step or expectation of the vector that goes beyond it. |

"The platform does something else on purpose" is not a ground by itself: the order of authority is the specifications first ([decisions.md, authority order](decisions/01-constraints-and-process.md#authority-order)). It becomes one when it shows that the text admits that reading.

## 4. The challenge file

A challenge is one Markdown file, `challenges/YYYY-MM-DD-<vector id>-<short-title>.md` (the folder is created with the first challenge). It holds:

1. **Vector**: the id, the suite version and commit it was read at (`<version>/<id>`, [docs/04 section 20.3](04-suite-design.md#20-releases-run-provenance-and-vector-ids-built)), and the run folder where the result was seen, if any.
2. **Ground**: one of the three above.
3. **Evidence**: the specification text quoted with file, line and pinned commit; for an implementation, the code reference at its recorded commit. Quote; do not paraphrase.
4. **Requested outcome**: fix, retire, move to CLARIFY, or change the expected code or step.
5. **Raised by** and **date**.
6. **State**: `open`, then the outcome and its reason (section 6). The file is kept after the challenge closes; it is the record.

## 5. While a challenge is open

A challenge changes no count by itself. The vector keeps running and keeps its channel. What the affected result shows is decided by the existing mechanisms:

- **A FAIL on the challenger's platform** gets an exception entry in [exceptions/exceptions.json](../exceptions/exceptions.json) (KNOWN-FAIL) with the class of the [vector review](../results/vector-review.md#classes) (B or C usually), a `ref` naming the challenge file, and the expiry the [expiry policy](../exceptions/expiry-policy.json) gives that surface. The entry's expiry is the deadline for an answer: if the challenge is not decided by then, the entry expires and the result is FAIL again, which forces the decision rather than letting it drift.
- **A verdict that is not reproducible** (the challenge is that the vector is flaky, not that it reads wrongly) goes in [exceptions/quarantine.json](../exceptions/quarantine.json) instead ([docs/04 section 18.9](04-suite-design.md#189-promotion-criteria-and-flaky-vectors-quarantine)).
- **When the maintainer agrees on reading** that the ground holds, the vector may be moved to CLARIFY at once (outcome 3 below), before the challenge file is closed.

## 6. Outcomes

Every challenge ends in exactly one of these, written into the file with the reason:

1. **Vector fixed.** The vector, its oracle and its mutant change in the generator; `npm run regen`; the exception entry goes (a stale entry fails the run, so it cannot be forgotten). The [CHANGELOG](../CHANGELOG.md) records the change under the next version.
2. **Vector retired.** The id gets a `retired` tombstone with the reason and the version, and is never reused ([docs/04 section 20.3](04-suite-design.md#20-releases-run-provenance-and-vector-ids-built)).
3. **Moved to CLARIFY (or to the candidate channel).** The vector stays and stops gating. Its `clarify` text states the open point, its `clarifyClass` is the upheld ground, `clarifySince` is the version it moved, and `clarifyQuestions` names the task-force question once one is drafted ([docs/04 section 18.10](04-suite-design.md#1810-clarify-classes-age-and-task-force-questions)). A vector that rests on a profile rule rather than specification text may move to the candidate channel instead.
4. **Challenge rejected.** The text supports the vector. The file quotes the text that decides it. The exception entry, if any, is re-read: it stays (with its class) or goes, and a draft issue for the platform may follow (marked `DRAFT, NOT FILED`; [CONTRIBUTING, recipe 4](../CONTRIBUTING.md#4-add-or-triage-a-difference-known-fail)).

A challenge on a `spec-inconsistent` ground whose answer only the task force can give ends in outcome 3, with the question drafted in [docs/09](09-draft-questions-for-task-force.md) or [the TF question list](proposals/tf-question-list.md), not sent.

## 7. Owner acknowledgement of an exception

An exception entry is written by the suite maintainer. Until the implementer who owns the platform has seen it and agreed that it describes their platform, the entry is unacknowledged: its `acknowledged` field is `null`, and the generated [vector review totals](../results/vector-review.md) and every KNOWN-FAIL reason show "unacknowledged". When an implementer acknowledges an entry (in a challenge file, or in any message the project owner records), the field becomes `{ "by": "<name or project>", "on": "YYYY-MM-DD", "ref": "<where it was acknowledged>" }`. Acknowledging an entry does not agree with the vector; challenging it is the way to disagree.

## 8. Future work

Per-implementer expectation files (each implementer keeps its own KNOWN-FAIL list, as web-platform-tests and test262 do, which the suite reads with `--exceptions`) are not built. For now one file holds every platform's entries ([decisions.md, exceptions](decisions/04-ceremonies-governance-and-exceptions.md#exceptions-known-fail)), and the [expiry policy](../exceptions/expiry-policy.json) is the one place that sets their dates.
