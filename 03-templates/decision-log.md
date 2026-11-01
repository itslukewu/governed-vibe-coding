# Decision Log — IDs, Open Items and Approved Decisions

> Registry of unresolved `OI-*` questions and human-approved `D-*` product decisions. This log
> documents approval **but never silently rewrites** an already frozen PDR or Feature Card.
> IDs are unique within each prefix, assigned monotonically and **never reused**. `F1`, `US-1.1`,
> `OI-1`, `D-1`, `F1-AC1`, `A-1` remain stable even if superseded.

## 1. ID registry

Reserve the next unused number by checking **all rows**, including closed, rejected and voided ones.
If the registry is inaccessible, the Agent must **propose** a provisional question without inventing
an official ID. Only authorized changes to this file allocate a definitive ID; coordinate concurrent
editors before assignment.

### Open Items registry

| ID | Related card / PDR | State | Summary | Resolved by |
| --- | --- | --- | --- | --- |
| OI-1 | F1 | open | <question> | — |

States: `open | resolved | closed_without_decision`. Do not delete rows after closure.

### Decision registry

| ID | Related OI | State | Short decision | Cards requiring re-approval | Incorporated into |
| --- | --- | --- | --- | --- | --- |
| D-1 | OI-1 | proposed | <proposal> | — | — |

States: `proposed | approved | rejected | superseded`. An `approved` row must also have
`approved_by`, `approved_at` and a complete record below. An approved decision still requires a
new pinned baseline before it can change what the Agent implements.

## 2. Open Item detail (copy once per OI)

### OI-1 — <short question>

```yaml
id: OI-1
status: open
card: F1                 # or PDR; use empty string if undecided
type: product_decision   # product_decision | spec_conflict | security_risk | infeasible | missing_info
question: ""
context: ""             # evidence, exact file/section/line references
options:
  - id: A
    consequence: ""
  - id: B
    consequence: ""
recommendation: ""      # optional implementing-agent suggestion; not an approval
blocks: []               # affected sections/tasks, not automatically entire project
resolved_by: ""         # D-1 or closed_without_decision rationale
```

## 3. Decision detail (copy once per D)

### D-1 — <short decision>

```yaml
id: D-1
related_oi: OI-1
status: proposed         # only human may set approved
question: ""
selected_option: ""
rationale: ""
impact:                  # name exact contract surfaces that change
  pdr_sections: []       # e.g. [2, 5]; non-empty means a new PDR spec_version is required
  card_sections: []
  card_ids: []           # cards that MUST be re-approved because of this decision.
                         # Write [] explicitly when no card is affected. A card not listed here
                         # stays valid against the PDR snapshot it was approved against.
  front_matter_fields: []
approved_by: ""          # human owner; required for approved
approved_at: ""          # ISO 8601; required for approved
supersedes: []           # earlier D-* if applicable
incorporation:
  spec_version: ""       # new PDR version if changed
  approved_spec_ref: ""  # pre-reference immutable snapshot
  cards: []              # [{id: F1, card_version: 2, approved_card_ref: "path@SHA"}]
```

## 4. Rules for use

1. Implementer may **propose** an `OI-*` with supporting facts, options and blocked work; it must
   not unilaterally approve `D-*`, assign conflicting numbers or modify this log without approval.
2. Human explicitly selects an option, records the `D-*` decision, and authorizes the affected PDR
   and/or Feature Card version change. If a decision affects an approved contract, apply the change
   to new versions and pin immutable snapshots using `01-pdr-review-freeze.md`.
3. Every new PDR `spec_version` cites the `D-*` that motivated it in the PDR's `decision_refs`, and
   each such `D-*` states `impact.card_ids` explicitly (an empty list is a valid, deliberate answer).
   Only the cards listed there need re-approval; all other cards stay bound to their pinned PDR snapshot.
4. Card `decision_refs` and PDR `decision_refs` are **frozen contract fields**. To add a `D-*`,
   approve and pin a new version. A log entry alone is not implementation permission.
5. Reviewer checks `D-*` status, human approval, `impact.card_ids`, and evidence of incorporation into
   the pinned contract; rejected/proposed decisions or post-freeze references cannot legitimize drift.
6. Never overwrite prior approved card/PDR versions, Open Items, decisions or their references.

> Remove the placeholder `OI-1`/`D-1` records before the first real project decision, or replace
> them with actual entries; the examples are illustrative and do **not** reserve live numbers.
