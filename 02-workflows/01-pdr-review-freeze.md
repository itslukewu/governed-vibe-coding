# 01 — PDR Review and Freeze (minimal Git workflow)

> This is the **owner-operated approval process**, not permission for an Agent to commit, push,
> merge, migrate a database or deploy. Every protected repository operation still requires the
> human owner's explicit approval in the conversation. Example commands below are educational.

## When to use it

- **Path A — first freeze:** the PDR is being approved for the first time (alone, or together with
  its first Feature Card).
- **Path B — later cards:** the PDR is already approved and you are approving another Feature Card.
- **Path C — changing an approved contract:** an approved `D-*` changes the PDR or a card.

## Authority before freezing

- PDR Sections 0–6: overall intent, scope, the list of Feature IDs, cross-feature rules and release gates.
- PDR Section 3 while **draft**: may contain `PROVISIONAL` feature detail and criteria; not binding.
- Feature Card Section 5 once **approved**: sole authoritative Given/When/Then criteria for that card.
- Before the PDR is approved, every provisional block in PDR Section 3 moves into its card draft or into
  PDR Section 7 (notes for cards not written yet). Never keep duplicate criterion bodies in both.
- PDR and card must agree. A genuine conflict other than delegated acceptance ownership becomes
  an Open Item (`OI-*`), not a silent priority choice.

## Frozen contract surfaces

| Document | Contract sections | Contract front-matter fields | Excluded working metadata |
| --- | --- | --- | --- |
| PDR | 0–6 | `id`, `project`, `spec_version`, `decision_refs` | `status`, `owner`, `feature_refs`, `approved_spec_ref`, `approved_at`, `open_items`, §7 |
| Feature Card | 1–6 | `id`, `title`, `card_version`, `spec_version`, `priority`, `user_stories`, `depends_on`, `decision_refs`, `acceptance_criteria` | `status`, `owner`, `approved_spec_ref`, `approved_card_ref`, `approved_at`, `open_items`, submissions, audit fields, §§7–9 |

`decision_refs` is deliberately frozen. Changing it after approval **cannot** make a new product
decision retroactively authorized. `feature_refs` and `owner` are operational: adding a card file or
changing the responsible person never requires re-approval.

## Path A — first freeze (two steps, no self-reference)

**Step 1 — Human approves contract text and captures a snapshot.** On an authorized feature branch,
confirm the documents being approved contain the agreed contract sections/fields, their own
`approved_*_ref` fields are **blank**, and PDR Section 3 contains no `PROVISIONAL` criteria. Capture the
**full SHA** of a commit containing these exact documents. This is the **pre-reference approval
commit**. It does not and cannot contain its own SHA.

```bash
# Run only when the owner has authorized these repository actions.
git add docs/spec/PDR.md docs/features/F1.md      # or only PDR.md if no card yet
git commit -m "approve PDR v0.1 + F1 v1"
git rev-parse HEAD  # copy the complete SHA, here called <APPROVAL_SHA>
```

**Step 2 — Human pins references in a later metadata update.**

```yaml
# docs/spec/PDR.md (metadata only)
approved_spec_ref: "docs/spec/PDR.md@<APPROVAL_SHA>"
approved_at: "<ISO-8601>"
status: approved

# docs/features/F1.md (metadata only, if approved in the same commit)
approved_spec_ref: "docs/spec/PDR.md@<APPROVAL_SHA>"
approved_card_ref: "docs/features/F1.md@<APPROVAL_SHA>"
approved_at: "<ISO-8601>"
status: approved
```

A second authorized commit may save these metadata edits; it is **not** the approved contract snapshot.

## Path B — approving a later card

The PDR is already approved and its `approved_spec_ref` is filled. That is expected; leave it as is.

1. Confirm the card's Feature ID already appears in the approved PDR Section 3. If it does not, this is a
   scope change: use Path C first.
2. Set the card's `spec_version` to the approved PDR version, and its `approved_spec_ref` to the **existing**
   PDR snapshot reference. Only the card's own `approved_card_ref` must be blank.
3. Commit the card alone and capture the SHA:

```bash
git add docs/features/F2.md
git commit -m "approve F2 v1"
git rev-parse HEAD  # <CARD_SHA>
```

4. Fill `approved_card_ref: "docs/features/F2.md@<CARD_SHA>"`, `approved_at` and `status: approved`.
   Optionally add `{id: F2, path: docs/features/F2.md}` to the PDR's `feature_refs` (operational).

## Verify before starting implementation

1. Resolve each ref to its pre-reference commit and read the files with `git show`:

   ```bash
   git show <SHA_from_approved_spec_ref>:docs/spec/PDR.md
   git show <SHA_from_approved_card_ref>:docs/features/F1.md
   ```
2. **Card:** compare its contract sections and frozen front-matter fields only against the current
   working card. Compare Markdown sections exactly after normalizing line endings; compare YAML frozen
   fields by parsed values (list contents and order). Do not compare whole files.
3. **PDR:** if the working PDR has the **same** `spec_version` as the card, compare its contract surface
   against the snapshot the card pins (unauthorized edits show up here). If the working PDR has a
   **newer** approved `spec_version`, do not treat the difference as drift for this card: check instead
   whether any approved `D-*` lists this card in `impact.card_ids`. If none does, the card remains valid
   against its pinned snapshot. If one does and the card has not been re-pinned, implementation is blocked.
4. Confirm card `acceptance_criteria` IDs exactly equal its Section 5 IDs, its Feature ID is listed in the
   pinned PDR Section 3, and every `D-*` in its `decision_refs` is approved and incorporated.
5. If any contract mismatch or missing ref remains, mark the freeze invalid and raise `OI-*`.
   Reviewers must not declare `pass` with an unverifiable baseline.

## Path C — changing an approved contract

- **PDR change:** record the `D-*` with an explicit `impact.card_ids` list (possibly empty). Increase
  `spec_version`, add the `D-*` to the PDR's `decision_refs`, and approve a new PDR snapshot with
  Path A steps. Preserve the old snapshot.
- **Affected cards** (only those in `impact.card_ids`): update `spec_version` and `approved_spec_ref` to
  the new PDR snapshot, increase `card_version`, re-approve with Path B, and retain old versions' evidence.
- **Card-only change:** increase `card_version` and re-approve that card with Path B; keep its
  `spec_version`/`approved_spec_ref` unchanged.
- **Unaffected cards** keep their existing pins. Do not alter an old baseline in place or treat a
  Decision Log entry as a substitute for pinning.

## Quick Start (the whole ritual in one sentence)

**Approve = finalize contract → commit with own ref blank → capture SHA → fill ref and status →
verify only the frozen fields/sections → build on the pinned contract.**
