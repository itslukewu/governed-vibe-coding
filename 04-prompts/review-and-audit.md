# review-and-audit.md — Independent Feature Audit

> Prompt from the Vibe Coding framework. Use **after a formal Feature Card submission**. Give this
> prompt to a human reviewer or an Agent **separate from the implementing Agent**, with read access to
> the approved PDR, Feature Card, Decision Log, exact submitted implementation and test evidence.
>
> This is a review protocol, **not** permission to modify product requirements, code, tests, branches,
> database schemas or deployments. It never authorizes commits, pushes or merges.

## Role and boundary

You are an **independent auditor**: use a fresh, separate context without the implementing Agent's
private reasoning or unsubmitted drafts. The same underlying model in a **new session** qualifies
only if it receives the approved contract, exact submitted artifacts and evidence, not the
implementation conversation or its hidden reasoning. Independence is an evidence boundary, not a
claim that two instances of the same model cannot make the same mistake.

Verify evidence; do not repair the implementation or rewrite acceptance criteria to make it pass. Only
the **human owner** approves product decisions, new baselines, `accepted`/`superseded` statuses, and
repository operations requiring explicit approval. The implementing Agent may provide
`submission_round` and `rework_reason` as statements, but **only the reviewer/human classifies and
counts** `rework_rounds`.

An approved requirement or decision is binding only at the version and immutable references pinned
in the card. Do not treat the latest branch, working spec, design mockup or chat text as an implicit
replacement for the approved baseline. If the baseline is missing or cannot be independently verified,
report **incomplete**, not `pass`.

## Inputs (do not assume missing inputs exist)

1. Feature Card: `id`, `card_version`, `spec_version`, `approved_spec_ref`, `approved_card_ref`,
   `approved_at`, `priority`, `user_stories`, `depends_on`, `decision_refs`, `acceptance_criteria`,
   `audit_status`, `spec_drift_count`, and Sections 1–9.
2. The PDR snapshot pinned by the card, the current working PDR, and `decision-log.md`.
3. Implementation snapshot named by `latest_submission_ref`, change summary, tests and raw evidence.
4. `first_submission_ref`, formal submission history and prior review results, if any.
5. Any relevant deployed-environment evidence **only if the current phase gate calls for it**.

If you have no access to the actual repository, tests, logs or deployment, distinguish inspected
facts from claims made by the implementing Agent. Do not claim to have independently run a test you
have only read about.

## Procedure

### 1. Verify the approved baseline (mandatory)

Follow `01-pdr-review-freeze.md`. Both approved references must point to **pre-reference approval
commits** (`<path>@<full SHA>`), which intentionally do not contain their own SHA. Resolve both snapshots
and use the exact approved PDR/card versions, never a moving branch.

**Card comparison:** extract (a) Sections **1–6** and (b) these front-matter **contract fields**:
`id`, `title`, `card_version`, `spec_version`, `priority`, `user_stories`, `depends_on`,
`decision_refs`, `acceptance_criteria`. Compare both surfaces with the current card. For YAML lists,
compare parsed value and order, not whitespace or comment formatting. For Sections 1–6, compare the
approved text exactly, apart from line-ending normalization. `owner`, `open_items`, `status`,
`approved_*_ref`, `approved_at`, `submission_*`, evidence and audit fields are **not** part of the
card's contract. Check criterion IDs in front matter are exactly the IDs in Section 5. A post-approval
addition of `decision_refs: [D-*]` is baseline drift, **not** an authorization.

**PDR comparison:** the card is bound to the PDR snapshot named in its `approved_spec_ref`.

- If the working PDR has the **same** `spec_version`, extract Sections **0–6** and the front-matter
  contract fields `id`, `project`, `spec_version`, `decision_refs` from both and compare. Ignore
  operational metadata (`status`, `owner`, `feature_refs`, `approved_spec_ref`, `approved_at`,
  `open_items`, Section 7). A difference here is unauthorized PDR drift.
- If the working PDR has a **newer** `spec_version`, do not report the difference as drift for this
  card. Confirm the newer version has its own approved snapshot, then check `decision-log.md`: if any
  approved `D-*` lists this card in `impact.card_ids` and the card was not re-pinned, report
  `card_reapproval_required: true` and do not mark `pass`.
- In the pinned PDR, confirm the card's Feature ID is listed in Section 3 and Section 3 contains no
  `PROVISIONAL` criteria. The card's Section 5 is authoritative for its acceptance criteria.

Verify every `D-*` in the card's `decision_refs` is approved and incorporated. If either snapshot is
missing or a contract surface differs, report `baseline_verification: failed | not_verified`, identify
exact fields/lines, **do not mark pass**, and escalate the disputed change to the human owner.

**Read-only Git illustration**:

```bash
git show <SHA_from_approved_spec_ref>:docs/spec/PDR.md
git show <SHA_from_approved_card_ref>:docs/features/F1.md
# Compare only the contract surfaces specified above.
# An approval commit does not contain its own approved_*_ref value.
```

### 2. Audit scope and product-decision boundaries

Compare the exact submission against the approved contract. Look for any change **beyond** the
approved contract affecting: (a) user-visible behavior or copy; (b) data model, permissions or data
visibility; (c) feature scope or non-goals; (d) acceptance criteria. Authorized implementation of an
already-approved behavior is **not** drift. For each suspected deviation, capture requirement and
code/test evidence, plus any valid `D-*` authorization.

**Spec Drift metric:** `spec_drift_count` is the count of **distinct unauthorized product decisions
independently detected** in the exact reviewed submission. Count each once even if it spans multiple
files. Set it to `0` only after completing the scope and evidence comparison; otherwise leave `null`.
The metric is detected drift, not a claim of exhaustive absence of unknown drift.

Do not call something approved merely because the implementing Agent labeled it a technical choice.
Security or infeasibility findings must be raised; do not silently rewrite the specification.

### 3. Audit acceptance evidence and coverage

Enumerate **every** approved criterion ID from the referenced card (`F<n>-AC<n>`). For each, record:

- `criterion_id`, `result`: `pass | fail | not_tested`;
- `evidence_ref` and whether it was **independently inspected**;
- concrete observed result and outstanding uncertainty.

Set `audit_coverage` to **only** criterion IDs actually reviewed with adequate evidence. Never copy
all expected IDs into `audit_coverage` unless all were checked. Compare tests and results to the
frozen Given/When/Then text; changing a test or criterion to match implementation does not count as
passing the original criterion. Missing tests, inaccessible evidence and unverified claims cannot be
marked `pass`.

### 4. Determine first-submission acceptance

Inspect the **first** immutable formal submission (`first_submission_ref`) for the **current approved
`card_version`**, not the latest submission. Use `first_submission_pass`:

- `pass`: the first submission met **all** frozen criteria with sufficient evidence;
- `fail`: the first submission was independently reviewed and at least one frozen criterion failed;
- `not_evaluated`: first snapshot, frozen baseline or adequate first-submission evidence cannot be
  verified. Do not convert missing evidence into `pass` or assume `fail` from missing evidence alone.

(Changed criteria within one `card_version` are caught by Step 1 as baseline drift.) Keep the original
first-submission result even when later rework passes. For a new approved `card_version`, start a
distinct version-scoped assessment and retain the previous version's audit and first-submission
references in archived snapshots/history.

### 5. Independently classify and count rework

Inspect the chronological series of submitted snapshots and review requests. The implementing
Agent's `rework_reason` is a **statement to check**, not a decision. For each resubmission, record
`submission_round`, associated review request, `rework_reason` statement, reviewer's classification
and evidence:

| Reviewer classification | Count in `rework_rounds`? |
| --- | --- |
| `requirements_misunderstanding` | Yes, when review-requested resubmission is evidenced |
| `implementation_deviation` | Yes, when review-requested resubmission is evidenced |
| `approved_scope_change` | No |
| `other` | No; explain |
| `inconclusive` | Do not guess; leave aggregate `rework_rounds` as `null` until resolved |

A rework round is **one resubmission after a review request** caused by misunderstanding of the
approved requirement or implementation deviation. It is **not** a number of code edits, commits,
files or lines changed. `rework_rounds` need not equal `submission_round - 1`. When all rounds are
classified, enter the total in the Feature Card's reviewer-owned field.

### 6. Final audit decision and write-back

Set `audit_status` to `not_started` before any review; `incomplete` when review has started but
required contract/submission/evidence checks remain unresolved; and `complete` only after all
mandatory checks are performed with enough evidence. Set `audit_result` to:

- `pass` **only** when the approved baseline is verified, no card re-approval is pending, the exact
  submitted implementation was reviewed, every frozen acceptance criterion has adequate evidence and
  passed, and there is no unresolved unauthorized product decision;
- `fail` if independently verified criterion failures or unauthorized product changes remain;
- `none` if the audit cannot be completed due to missing baseline, submission or sufficient evidence;
  report `audit_status: incomplete`. `none` is **not** an acceptance result. Proven baseline tampering
  or an unauthorized product change can be `fail` even while the rest of the audit is incomplete;
  explain the limited coverage.

Only the independent reviewer writes `audit_status`, `audit_result`, `audit_coverage`,
`spec_drift_count`, `first_submission_pass`, `rework_rounds`, Section 8 of the card, and
review/classification entries in Section 9. A human owner separately decides whether the card becomes
`accepted`, `changes_requested`, or needs a new approved version. Review write-back does **not**
authorize a Git commit or other protected operation.

## Required output (keep these field names stable)

Return a concise explanation followed by a YAML report using this shape. Use `null` for values that
cannot be determined; do not invent sources or fill gaps with guesses.

```yaml
card_id: F1
card_version: 1
spec_version: v0.1
approved_spec_ref: "<exact ref>"
approved_card_ref: "<path>@<pre-reference approval SHA>"
baseline_verification: verified  # verified | failed | not_verified
card_contract_match: true        # true | false | null; Sections 1-6 + frozen front matter
pdr_contract_match: true         # true | false | null; only when working PDR has the same spec_version
working_pdr_newer: false         # true when the working PDR has a newer approved spec_version
card_reapproval_required: false  # true when an approved D-* lists this card in impact.card_ids, unpinned
reviewed_submission_ref: "<exact ref>"
audit_status: incomplete  # not_started | incomplete | complete
audit_result: none        # none | pass | fail
audit_coverage: []        # only IDs independently reviewed with evidence
criterion_results:
  - criterion_id: F1-AC1
    result: not_tested    # pass | fail | not_tested
    evidence_ref: ""
    independently_inspected: false
spec_drift_count: null    # set 0 only after a complete independent check
detected_spec_drift: []   # one entry per distinct unauthorized product decision
first_submission_ref: "<exact ref>"
first_submission_pass: not_evaluated  # pass | fail | not_evaluated
rework_classifications:
  - submission_round: 2
    rework_reason: "<implementer's statement>"
    reviewer_classification: inconclusive  # see Section 5
    counts_as_rework: null  # true | false | null
    evidence_ref: ""
rework_rounds: null       # reviewer-only count, null until classified
open_items: []
recommendation: "<accept | request_changes | request_decision | cannot_assess; advisory only>"
```

**Reviewer checklist:** Confirm the submission and card references do not point to moving branches;
confirm criteria have not been reworded; show missing coverage, disagreement with the implementer's
claimed test results, and each unresolved `OI-*`. Do not mark `accepted`; report what the evidence
supports and what the human still needs to decide.
