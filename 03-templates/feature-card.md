---
# ── Feature Card ── Vibe Coding framework template
# Front matter = machine-readable contract. Body = human-readable detail.
# Field names are stable; do not rename them. Leave a field empty rather than deleting it.

id: F1                      # never reused, even after the card is closed; same F-ID as in PDR Section 3
title: ""
card_version: 1             # +1 every time an approved card changes
spec_version: v0.1          # PDR version this card was approved against
approved_spec_ref: ""        # PDR path@SHA of the PDR snapshot this card is bound to
approved_card_ref: ""        # card path@SHA of pre-reference approval commit
approved_at: ""              # ISO 8601 approval date/time; set by the human owner
status: draft               # see "Status lifecycle" below
priority: P0                # P0 must-have | P1 if time allows | P2 next version
owner: ""                   # human who approves and accepts; operational, not contract
user_stories: []            # e.g. [US-1.1, US-1.2]
depends_on: []              # other cards, e.g. [F0]
decision_refs: []           # e.g. [D-3]
open_items: []              # e.g. [OI-2]; agent may append OI IDs it proposed, never remove
acceptance_criteria:        # ids only; full text lives in Section 5
  - F1-AC1
  - F1-AC2
first_submission_ref: ""     # durable reference to first formal implementation submission
latest_submission_ref: ""    # updated on each formal submission
submission_round: 0         # 0 before submission, 1 at first submission
rework_reason: ""           # implementer's factual account; not an audit classification
rework_rounds: null          # reviewer-only count; null until independently assessed
spec_drift_count: null       # reviewer only; null until complete scoped drift check
audit_status: not_started    # not_started | incomplete | complete; reviewer only
audit_result: none          # none | pass | fail; reviewer only
audit_coverage: []          # criterion IDs actually reviewed; not assumed from the card
first_submission_pass: not_evaluated  # pass | fail | not_evaluated; reviewer only
---

# F1: <Feature title>

## 1. Summary

One or two sentences: what this feature does for the user, and what it deliberately does not do.

Approval and baseline pinning: see `01-pdr-review-freeze.md` (owner action; not part of this
feature's contract requirements).

## 2. Scope

**In scope**

- 

**Non-goals** (the agent must not build these, including placeholder UI)

- 

## 3. Preconditions

- User state: (signed in? which role?)
- Permissions or consents required:
- Cards that must be completed first:

## 4. Behavior rules

Write the rules as the system should behave, in order. Number them so they can be referenced.

1. **Input:** what the user provides, and what data is read (object + fields).
2. **Processing:** the steps the system takes.
3. **Output / state change:** what is created or changed, and what the user sees.

**Edge cases**

- Empty data:
- Limits (max length, max count):
- Duplicate submission / concurrency:
- Time zones, locales:

**Error handling**

- Network or service failure: what the user sees; what happens to partially saved data.
- Permission denied:
- Invalid input:

## 5. Acceptance criteria

Each criterion must be observable and testable. Cover at least one main path and one failure path.
Provisional criteria drafted in PDR Section 3 are a drafting source only; after approval, **this
card's Section 5 is the sole source of the feature's acceptance criteria**. The front-matter
`acceptance_criteria` list must exactly match the criterion IDs in this section (no missing or extra IDs).

Once the card is `approved`, criteria are frozen: changing them requires a human-approved Decision Log
entry, a new `card_version`, and a new immutable `approved_card_ref`. Keep previous snapshots; do not
overwrite them. A newer PDR version requires re-approving this card **only** when an approved `D-*`
lists this card in `impact.card_ids`; otherwise the card stays bound to its pinned PDR snapshot.
A Decision Log entry alone never authorizes implementation against an older pinned baseline.

| ID | Given | When | Then |
| --- | --- | --- | --- |
| F1-AC1 | | | |
| F1-AC2 | | | |

## 6. Implementation constraints

Product-level limits on how it may be built (not a design for it).

- Data that must not leave the system / third parties not allowed:
- Performance expectations:
- Must reuse: (existing components, APIs)

---

*Contract surface = Sections 1–6 plus front-matter `id`, `title`, `card_version`, `spec_version`,
`priority`, `user_stories`, `depends_on`, `decision_refs` and `acceptance_criteria`. Changing even one
approved `decision_refs` entry requires a new approved card version and snapshot. Other front-matter
fields (including `owner` and `open_items`) are operational metadata, not authorization for new product
behavior. Sections 7–9 record execution and review evidence.*

## 7. Test evidence

| Criterion | Result | Evidence |
| --- | --- | --- |
| F1-AC1 | pass / fail / not_tested | test name, run output, or manual steps + observed result |
| F1-AC2 | | |

## 8. Audit result

Filled in by an **independent reviewer** (human or a separate review agent using
`review-and-audit.md`), not by the implementing agent. The reviewer may identify and document drift;
only the human owner can decide whether to change the approved spec or accept the feature.

- Approved baseline checked: `approved_spec_ref` / `approved_card_ref` (verified / not verified)
- Implementation reviewed: `latest_submission_ref`
- Audit state: `not_started | incomplete | complete` (also set `audit_status`; differentiate an
  unstarted review from missing evidence during a started review)
- Result: `none | pass | fail` (also set `audit_result`); do not record `pass` unless every criterion is
  reviewed with adequate evidence and no unresolved unauthorized product changes remain
- Spec drift detected: count of **distinct unauthorized product decisions**, with IDs/locations (do not
  count the same deviation twice because it appears in multiple files; set `spec_drift_count` to
  the count after a complete drift check, otherwise keep `null`)
- Criteria covered by the audit: explicit IDs (also set `audit_coverage`); flag missing coverage
- First-submission acceptance pass: pass / fail / not_evaluated (also set `first_submission_pass`).
  `pass` means the **first** formal submission passed **all** frozen criteria with evidence. If audit
  coverage or the baseline is unavailable, use `not_evaluated`, not `pass`.
- Implementer's rework statement: read `rework_reason` but do not treat it as the authoritative cause
- Rework rounds: **reviewer independently classifies** each review-requested resubmission; count only
  rounds caused by requirements misunderstanding or implementation deviation, not code edits,
  individual files or approved scope changes (also set `rework_rounds`)
- Notes / unresolved items:

## 9. History

Newest first. One row per approval, submission, review, status or version change. Retain durable
references to the original approved snapshots and the first submission; a later submission must never
overwrite `first_submission_ref`. `submission_round` starts at 1 on first formal submission and
increases once per resubmission. The Agent records `rework_reason` as its factual account; this
statement is not a classification. Only the independent reviewer counts `rework_rounds` and
`spec_drift_count` under Section 8 and `review-and-audit.md`. Agent updates submission entries; reviewer
updates classification and audit entries; the human owner approves baselines and final acceptance. For
a new approved `card_version`, retain the previous version's first-submission and audit history in its
archived snapshot, then track submission/audit fields for the newly approved version separately.

| Date (ISO 8601) | card_version | Status | Evidence or baseline ref | Note |
| --- | --- | --- | --- | --- |
| | 1 | draft | | created |

---

<details>
<summary>Reference: status lifecycle and ID rules</summary>

**Status lifecycle** (same values in every framework document)

```
draft → in_review → approved → in_progress → submitted → accepted
                                     ↕                  ↘
                                  blocked           changes_requested
                                                          │
                                                          └→ in_progress

in_progress → blocked → in_progress              (resolved Open Item)
any non-superseded state → superseded            (human approves replacement only)
```

| Status | Meaning | Who sets it |
| --- | --- | --- |
| `draft` | Being written | Human |
| `in_review` | Ready for review; not yet binding | Human |
| `approved` | Frozen contract; agent may start | Human |
| `in_progress` | Agent is implementing | Agent |
| `blocked` | Waiting on an Open Item | Agent or human |
| `submitted` | Implementation and change summary handed in | Agent |
| `changes_requested` | Review failed; rework needed | Human |
| `accepted` | Passed review; done | Human only |
| `superseded` | Replaced by a newer card; kept for history | Human |

**ID rules**

- Cards `F<n>`, stories `US-<journey>.<n>`, criteria `F<n>-AC<n>`, open items `OI-<n>`,
  decisions `D-<n>`, assumptions `A-<n>`.
- IDs are never reused or renumbered, even when an item is closed or deleted.
- Changing an approved card's requirements creates a new `card_version` and a new approved snapshot;
  the earlier approved version and its criterion text remain retrievable.
- `approved_spec_ref` / `approved_card_ref` identify **pre-reference approval commits**; compare
  only the explicitly defined contract surfaces, not entire files. See `01-pdr-review-freeze.md`.
- A card's `decision_refs` and criterion IDs are frozen contract fields, not mutable audit metadata.
- `first_submission_ref` is write-once **within each approved `card_version`**; prior versions retain
  their first-submission evidence in their archived snapshots/history. `latest_submission_ref` changes
  at each formal submission of the active version.
- `submission_round` and `rework_reason` are Agent statements; `rework_rounds` and all audit fields
  belong to an independent reviewer.
- Approved product requirements, baseline references and decisions can be changed only by the human
  owner through a new authorized version; implementation metadata may be recorded without changing them.

</details>
