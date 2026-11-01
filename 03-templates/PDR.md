---
# PDR — Product Definition & Rules (framework v1 template)
# Stable front-matter keys. Fill values; do not rename or delete keys.
id: PDR
project: ""
spec_version: v0.1          # version of this PDR; increment on an approved contract change
status: draft              # draft | in_review | approved | superseded
owner: ""                  # human responsible for approval; operational, not contract
feature_refs: []           # card file locations, e.g. [{id: F1, path: docs/features/F1.md}];
                           # operational index, not contract (features themselves are defined in §3)
decision_refs: []          # e.g. [D-1]; frozen contract field
approved_spec_ref: ""      # path@full-SHA of pre-reference approval commit
approved_at: ""            # ISO 8601 timestamp set by human owner
open_items: []             # mutable registry references e.g. [OI-1], not authorization
---

# PDR — <Project / product name>

> PDR = **Product Definition & Rules** in this framework, not the conventional Product Requirements
> Document (PRD). A PRD describes what to build; a PDR also fixes the rules an AI agent must not change
> without human approval. Approval and snapshot instructions live in `01-pdr-review-freeze.md`.

## 0. Product intent and priorities

- Problem (who has it, in what situation, how they cope today):
- Goal of this version (one main goal):
- Success signal (observable, checkable):
- Hard time constraints:
- Priority meanings: **P0** = required for this release; **P1** = only if P0 is safe and complete;
  **P2** = post-release candidate, not tacit permission to implement.
- Rule: “not specified” does not mean “intentionally cut”; flag unclear scope as `OI-*`.

## 1. Users, roles and journeys

**Personas** (1–2; region, devices, frequency of use):

- 

**Journeys** (start → end, including what happens on failure, refusal or quitting midway):

- J1 <name>: <start> → <end>. Failure / refusal handling:

| Story ID | Persona / role | Story | Feature |
| --- | --- | --- | --- |
| US-1.1 | | As a <user>, I want <action>, so that <value>. | F1 |
| US-1.2 | | As a <user>, when <failure case>, I want <system behavior>. | F1 |

- Journey ordering rules (steps that must happen in a fixed order, or "none"):
- Intended / excluded audiences:

## 2. Scope and non-goals

| Priority | Feature ID | Capability | Reason / boundary |
| --- | --- | --- | --- |
| P0 | F1 | | |

**Explicit non-goals** (not even placeholder UI, unless later approved):

- 

**Deferred to a later version** (may be prepared behind an off-by-default flag only if stated here):

- 

**Assumptions / dependencies** (`A-<n>`; do not silently convert them into product requirements):

- A-1:

## 3. Feature list and validation map

Every capability the product will have gets a stable **Feature ID** here (`F<n>`), including P1/P2.
Adding a new Feature ID is a scope change and needs a new approved `spec_version`. Writing or approving
a Feature Card for a Feature ID already listed here is **not** a PDR change.

| Feature ID | Priority | What must be validated (one line) |
| --- | --- | --- |
| F1 | P0 | |

**During `draft` only:** provisional detail and acceptance criteria may be written under each Feature
ID, clearly labeled `PROVISIONAL`. They are drafting notes, not executable or authoritative.

**Before approving this PDR:** move every provisional block either into its Feature Card draft, or into
Section 7 as notes for a card not written yet. After approval, a Feature Card's Section 5 is the **only
binding source** of that feature's acceptance criteria. No approval while this section still contains
provisional criteria. Cross-feature release checks belong in Section 6.

## 4. Cross-feature product and object rules

- Global product behavior / invariants (number each rule):
- Shared objects and relationships (what they mean, not a prematurely fixed database schema):
- Shared error / permission behavior:

If a Feature Card contradicts a global rule, raise an `OI-*`; do not silently resolve by priority.

## 5. Data, privacy and safety boundaries

| Data class | Examples | Storage condition | AI-use condition |
| --- | --- | --- | --- |
| | | | |

- Roles, visibility, consent (when asked) and retention rules:
- What happens if the user refuses consent (refusal must not make the product unusable):
- What happens on deletion / consent withdrawal:
- Allowed / forbidden external data transfers:
- Safety, regulatory or security assumptions requiring human or external review:
- Degraded behavior if permission, API or data is unavailable:

## 6. Constraints and release gates

**Technical and experience constraints**

| Item | Choice | Not allowed |
| --- | --- | --- |
| Tech stack | | |
| Platforms | | |
| Environments | | |
| Performance | | |
| Design | | |

**Release gates**

- [ ] All P0 Feature Cards `accepted` (main + failure paths evidenced)
- [ ] Every journey in Section 1 can be completed end to end
- [ ] No non-goal implemented, including placeholder UI
- [ ] Data visibility checks pass (user A cannot read user B's data)
- [ ] Refusing consent still leaves a usable product
- [ ] Empty, loading and error states exist for every screen
- [ ] Tested on target platforms / real devices
- Rollback or stop conditions (tool-agnostic):
- Out-of-scope changes require a new decision, new version and new approved baseline.

---

## 7. Working notes and open items (not contract)

- Open items (`OI-*`, registered in `decision-log.md`):
- Provisional criteria for features whose cards are not written yet:
- Recommended advanced modules and which complexity trigger fired:
- Evidence links / discussion context:

## Reference — Contract and snapshot semantics

- **Frozen PDR contract:** Sections **0–6** plus front matter `id`, `project`, `spec_version`,
  `decision_refs`. Compare these against the pre-reference approval commit. Changing them requires a
  newly approved `spec_version`, citing the `D-*` that motivated it.
- **Operational metadata:** `status`, `owner`, `feature_refs`, `approved_spec_ref`, `approved_at`,
  `open_items` and Section 7 may change with proper role authorization; changes do not authorize
  product scope. Owner changes must be explicitly authorized by a human.
- **Cards stay bound to the PDR snapshot they were approved against.** A new PDR version forces a
  card re-approval only when an approved `D-*` lists that card in `impact.card_ids`.
- **Two-step approval:** commit the approved contract with `approved_spec_ref` still blank; after
  capturing the SHA, fill `approved_spec_ref: "<path>@<SHA>"`, `approved_at` and `status: approved`.
  See `01-pdr-review-freeze.md`.
