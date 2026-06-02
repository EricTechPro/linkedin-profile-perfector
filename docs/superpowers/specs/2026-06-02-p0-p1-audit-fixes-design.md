# Spec — P0+P1 Audit Fixes for `linkedin-profile-perfector`

**Date:** 2026-06-02
**Branch:** `fix/p0-p1-audit-fixes`
**Source:** Two-agent review (skill auditor + 4-persona QA tester), aggregated into a
prioritized P0/P1/P2 list. This spec covers **3 P0 + 5 P1** items. P2 items are deferred,
except those already satisfied (`runs/` is gitignored).

## Goal

Fix correctness/contradiction defects (P0) and quality gaps (P1) in the published skill
without changing its architecture, adding dependencies, or introducing machine-specific
values. Everything stays MIT-licensed and portable.

## Scope — fixes

### P0 — correctness

- **P0-1 · Tokenize HTML state colors.** `assets/report-template.html` hardcodes
  `class="overall-score score-amber"` (line 345) and `class="scan-verdict scan-skip"`
  (line 354), so every report renders amber + red-SKIP regardless of the real score.
  Fix: make the class a token (`{{SCORE_CLASS_BEFORE}}`, `{{SCORE_CLASS_AFTER}}`,
  `{{SCAN_CLASS}}`) and add a mapping table to SKILL.md Step G:
  - overall score ≥75 → `score-green`, 50–74 → `score-amber`, <50 → `score-red`
  - verdict CLICK → `scan-click`, SKIP → `scan-skip`

- **P0-2 · One canonical 6-second scan.** The scan is fully defined in two files that
  already disagree (headline "truncated at ~220 chars" vs. "read in full"). Make
  `industry-best-practices.md §6.1` the single canonical definition (resolve the
  truncation wording there), reduce the duplicate in `scoring-framework.md` to a pointer
  plus the inline output-format block, and add a precedence line to SKILL.md Step D.

- **P0-3 · One unit for About length.** SKILL.md and `copy-playbooks.md` use characters;
  `scoring-framework.md` uses words (300–400 / under 100 / over 600). Restate the
  scoring-framework bands and deductions in characters, aligned to SKILL.md's
  1,500–2,000 (2,600 max), with a one-line chars↔words note so the audit can't reward a
  length the rewrite then trims.

### P1 — quality

- **P1-4 · Anti-fabrication guardrail.** Add a bolded rule to SKILL.md Step E and
  `copy-playbooks.md §1.1`: never introduce a metric, employer, title, date, or
  achievement absent from the user's material; insert a `[add metric: …]` placeholder
  instead. Mark the playbook's worked-example numbers as illustrative.

- **P1-5 · Wire benchmarks into scoring severity.** SKILL.md Step D: adjust deduction
  severity using `benchmarks.md` industry notes before finalizing each section score,
  and cite the industry context in the scorecard's Top-Fix column.

- **P1-6 · Guarantee profile-infrastructure coverage.** Add a "Profile Infrastructure"
  checklist block (`{{PROFILE_INFRA_CHECKLIST}}`) to both templates covering custom URL,
  banner, Featured, Open-to-Work, and recommendations strategy, and require all scored
  sections to appear as explicit scorecard rows.

- **P1-7 · Keyword × surface gap map.** Replace the flat `{{KEYWORDS}}` chip list with a
  `{{KEYWORD_GAP_TABLE}}` (keyword | in headline? | in about? | in a bullet? | in skills?)
  driven by the placement priority already in `copy-playbooks.md`.

- **P1-8 · Before → after scoring.** Show a projected score next to the current one:
  overall (`{{OVERALL_SCORE_BEFORE}} → {{OVERALL_SCORE_AFTER}}`) and a per-section
  "Now → Projected" pair in the scorecard. The skill re-scores its own optimized copy on
  the same rubric, labeled **"(projected)"** so it stays honest. The projected score must
  be defensible from the rewritten copy (ties to P1-4 — no inflation).

## Non-goals (deferred P2)

Mode/weights annotation under the score, "summary" vs About naming, checklist cap
10–12 vs ≤10, static HTML checkbox styling, md-vs-html verdict consistency.

## Verification

After the edits, re-run all 4 personas (job / consulting / creator / brand), fill both
templates, headless-render each `report.html`, and confirm:

1. Color classes vary by score — a high-score persona renders green/CLICK, a low one
   amber-or-red/SKIP (the original bug is gone).
2. The keyword gap table and profile-infrastructure block populate.
3. Per-section and overall before→after scores render, with after ≥ before and the
   projected number justifiable from the rewritten copy.
4. No `{{…}}` tokens remain; no fabricated metrics.

Screenshots saved as evidence.
