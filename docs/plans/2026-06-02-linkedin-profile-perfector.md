# linkedin-profile-perfector Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a single public, open-source Claude Code skill that turns a user's profile material + a guided adaptive intake into a recruiter-grade LinkedIn audit, copy-paste-ready rewrites, and a slim action checklist, output as report.md + styled report.html.

**Architecture:** Markdown-driven Claude-native skill (no Python scripts). `SKILL.md` orchestrates a 7-step adaptive intake (dropdowns + progress bar), then Claude reads the user's material directly (PDF/resume/URL/text via the Read/WebFetch tools), scores it with `references/scoring-framework.md`, rewrites copy with `references/copy-playbooks.md`, and renders the two report files from `assets/` templates. Four goal modes (branding / job / creator / consulting) re-weight every stage. Both upstream sources are MIT.

**Tech Stack:** Markdown (skill + references), HTML/CSS (report template), git + GitHub CLI, `npx skills` for distribution. No runtime language deps.

**Deviation from spec §5/§8:** the two Python scripts (`parse_material.py`, `render_report.py`) are dropped in favor of Claude-native parsing/rendering for robustness + portability. Update spec §5/§8/§9 to match during Task 1.

---

## File Structure

```
linkedin-profile-perfector/
├── README.md                  what it does · install (npx skills add) · run in Claude Code · demo
├── INSTALL.md                 setup steps · requirements · honest-limits section
├── LICENSE                    MIT
├── ATTRIBUTION.md             credits optimizer + schwepps (both MIT) + original layers
├── VERSION                    0.1.0
├── .gitignore                 OS/editor cruft + sample run outputs
├── SKILL.md                   orchestration: intake → parse → classify → audit → rewrite → plan → render
├── references/
│   ├── intake-questions.md          7-step adaptive flow · mode routing · progress-bar spec
│   ├── scoring-framework.md         material-aware 0–10 per-section scoring (from schwepps)
│   ├── benchmarks.md                industry benchmarks · SSI ranges (context, not faked)
│   ├── copy-playbooks.md            headline/About/experience formulas + embedded copy rules
│   └── industry-best-practices.md   curated best-of-industry + recruiter 6-sec scan
├── assets/
│   ├── report-template.md
│   └── report-template.html
└── docs/
    ├── specs/2026-06-02-linkedin-profile-perfector-design.md   (exists)
    └── plans/2026-06-02-linkedin-profile-perfector.md          (this file)
```

Validation note: since this is a content/skill repo (not a code library), "tests" are **dry runs** of the skill against a sample profile, asserting the produced report has the required sections and no unfilled placeholders. Task 13 is the end-to-end validation.

---

### Task 1: Scaffold repo + update spec to match Claude-native architecture

**Files:**
- Create: `linkedin-profile-perfector/.gitignore`
- Create: `linkedin-profile-perfector/VERSION`
- Create: `linkedin-profile-perfector/LICENSE`
- Modify: `linkedin-profile-perfector/docs/specs/2026-06-02-linkedin-profile-perfector-design.md` (§5 drop scripts, §8 components → Claude-native, §9 testing → dry runs)

- [ ] **Step 1: Initialize git repo**

```bash
cd /Users/erictech/Desktop/EricTechOS/linkedin-profile-perfector
git init -b main
```

- [ ] **Step 2: Write `.gitignore`**

```
.DS_Store
*.swp
/runs/
/sample-runs/*.local.*
__pycache__/
.venv/
```

- [ ] **Step 3: Write `VERSION`**

```
0.1.0
```

- [ ] **Step 4: Write `LICENSE`** — standard MIT text, copyright line:

```
MIT License

Copyright (c) 2026 EricTechPro

Permission is hereby granted, free of charge, to any person obtaining a copy
... (full standard MIT body) ...
```

- [ ] **Step 5: Update spec** — in the design spec, replace `scripts/parse_material.py` + `render_report.py` references (§5 tree, §8 components, §9 tests) with the Claude-native approach: Claude reads material via Read/WebFetch; renders report.md/report.html by filling `assets/` templates; validation is dry runs. Remove the `scripts/` directory from the §5 tree.

- [ ] **Step 6: Commit**

```bash
git add -A
git commit -m "chore: scaffold linkedin-profile-perfector repo (MIT, v0.1.0)"
```

---

### Task 2: Write `SKILL.md` (orchestration)

**Files:**
- Create: `linkedin-profile-perfector/SKILL.md`

- [ ] **Step 1: Write the skill frontmatter + trigger**

Frontmatter `name: linkedin-profile-perfector`, and a `description` that triggers on: "optimize my LinkedIn", "LinkedIn profile", "improve my LinkedIn", "LinkedIn headline/About/experience", "personal branding", "LinkedIn for job search", "LinkedIn for consulting/clients", "grow my LinkedIn". Mention it produces an audit + rewrites + report from the user's profile material.

- [ ] **Step 2: Write the orchestration body** with these exact ordered sections:

1. **Overview** — what it does, the 4 goal modes, output (report.md + report.html). One-paragraph honest-limits pointer to INSTALL.md.
2. **Step A — Run the adaptive intake.** Instruct: load `references/intake-questions.md` and run the 7-step flow. Use the AskUserQuestion tool to render each question as a **dropdown** with generated options + a "🤖 Recommend for me" option + Other. Print the progress bar before each step. Auto-fill/skip Steps 3–4 when Step-2 material already answers them, marking ⏭️ in the progress bar.
3. **Step B — Ingest material.** Read the LinkedIn PDF via the Read tool; resumes via Read; website/portfolio URLs via WebFetch; pasted text/notes inline. Merge into a working profile model: headline, About, experience[], skills[], education[], recommendations, role/industry/audience signals, achievements, keywords.
4. **Step C — Classify.** Industry · profile type · target audience (from material + intake). Drives benchmark selection.
5. **Step D — Audit.** Load `references/scoring-framework.md` + `references/benchmarks.md` + `references/industry-best-practices.md`. Score each section 0–10, apply the recruiter 6-second-scan, flag gaps. Mark live-only metrics as "not available — check at linkedin.com/sales/ssi".
6. **Step E — Rewrite.** Load `references/copy-playbooks.md`. Produce Current → Optimized for headline, About, every experience role (short, bulleted, keyword-rich, metric-driven), and skills. Tune emphasis to the selected mode.
7. **Step F — Action checklist.** Slim, mode-specific prioritized checklist.
8. **Step G — Render.** Fill `assets/report-template.md` → `runs/<date>-<slug>/report.md` and `assets/report-template.html` → `.../report.html`. Tell the user both paths and offer to open the HTML.

- [ ] **Step 3: Verify no placeholders** — read the file; confirm every Step A–G names the exact reference/asset file it loads and the exact output path. Fix any vague instruction.

- [ ] **Step 4: Commit**

```bash
git add SKILL.md
git commit -m "feat: add SKILL.md orchestration (intake→audit→rewrite→render)"
```

---

### Task 3: Write `references/intake-questions.md`

**Files:**
- Create: `linkedin-profile-perfector/references/intake-questions.md`

- [ ] **Step 1: Write the progress-bar spec** — exact render format (copy verbatim into the file):

```
🟩🟩🟩⏭️🟩⬜⬜   Step 5 of 7
✅ 1. Goal — Job search
✅ 2. Material — PDF + website + resume
✅ 3. Target role — Senior PM  (read from your profile)
⏭️ 4. Industry — B2B SaaS  (auto-filled, skipped)
▶️ 5. Audience …
```
Legend: 🟩 answered · ▶️ current · ⏭️ auto-filled/skipped (with one-line "why") · ⬜ pending.

- [ ] **Step 2: Write the 7 steps** — each with: emoji, exact question text, how options are generated, the always-include "🤖 Recommend for me" + "Other" options, and skip/auto-fill rules. Use this exact table content:

| # | Emoji | Question | Options |
|---|---|---|---|
| 1 | 🎯 | "What's the #1 thing you want this profile to do?" | Personal brand · Land a job · Grow as creator · Win consulting clients · 🤖 Help me decide — always asked, sets mode |
| 2 | 📥 | "Drop everything you've got — in one place." | Multi-select: LinkedIn PDF · Resume · Website URL · Portfolio/projects · Profile text · Wiki/notes · Describe me — always asked; then prompt for path/URL |
| 3 | 🧑‍💼 | mode-framed: job→"title you want to land"; consulting→"service you offer"; brand/creator→"what you want to be known for" | options generated from material + 🤖 + Other; **auto-fill & skip if Step 2 reveals it** |
| 4 | 🏢 | "What industry / field is that in?" | **skip if material shows it**; else generated options + Other |
| 5 | 👥 | "Who most needs to be impressed landing on your profile?" | mode+context recommended: branding→business owners/peers; job→recruiters/hiring managers; creator→niche audience; consulting→potential clients; + 🤖 |
| 6 | ⭐ | "Your top 2–3 achievements / selling points?" | skill proposes top ones found in material; user picks/edits + adds own |
| 7 | 🔑 | "Keywords to rank for" | **auto-generated** from goal+role+industry via copy-playbooks; user confirms/tweaks; optional paste a target job |

- [ ] **Step 3: Write the auto-fill/skip algorithm** — prose: after Step 2 ingest, attempt to answer 3,4,5,6,7 from material; if confidence high, present as pre-filled confirmation (single keystroke to accept) or skip with ⏭️; if low, ask with generated options.

- [ ] **Step 4: Commit**

```bash
git add references/intake-questions.md
git commit -m "feat: add adaptive 7-step intake with progress bar"
```

---

### Task 4: Write `references/scoring-framework.md`

**Files:**
- Create: `linkedin-profile-perfector/references/scoring-framework.md`

- [ ] **Step 1: Write per-section 0–10 rubrics** for: Photo, Banner, Headline, About, Experience, Skills, Recommendations, (Activity — note: limited from PDF). Adapt from schwepps's `scoring_framework.md` (MIT). Each rubric: a 9-10 / 7-8 / 5-6 / 3-4 / 1-2 / 0 criteria table + a "start at 10, subtract for issues" checklist. For PDF-sourced material, mark Photo/Banner/Activity as "score only if image/metrics provided; else flag as not-assessable-from-PDF".

- [ ] **Step 2: Write the weighted overall score** — section weights summing to 100, mode-adjusted (e.g. job mode weights Headline+Experience+keywords higher; creator mode weights About+Activity; consulting weights About+proof). Give the exact weight tables per mode.

- [ ] **Step 3: Write the recruiter 6-second-scan rule** — a fast pass: "In 6 seconds from headline + first About line + current role, would the target audience click or skip?" with skip-triggers (vague headline, no value prop, no keywords, "seeking opportunities") and click-triggers.

- [ ] **Step 4: Commit**

```bash
git add references/scoring-framework.md
git commit -m "feat: add material-aware 0–10 scoring framework + recruiter scan"
```

---

### Task 5: Write `references/benchmarks.md`

**Files:**
- Create: `linkedin-profile-perfector/references/benchmarks.md`

- [ ] **Step 1: Write benchmark tables** adapted from schwepps `metrics_benchmarks.md` (MIT): SSI ranges (Elite 80-100 … Low <40), profile-view/connection/recommendation targets, engagement-rate formula `(R+C+S)/impressions×100` with 3%+ target. Each clearly labeled **"context for the user — NOT computed from a PDF"** where it needs live data.
- [ ] **Step 2: Write industry-specific notes** — short per-industry benchmark adjustments (tech, finance, healthcare, marketing, consulting, creative) used to contextualize advice.
- [ ] **Step 3: Commit**

```bash
git add references/benchmarks.md
git commit -m "feat: add industry benchmarks reference (context, not fabricated)"
```

---

### Task 6: Write `references/copy-playbooks.md`

**Files:**
- Create: `linkedin-profile-perfector/references/copy-playbooks.md`

- [ ] **Step 1: Write the embedded copy rules** (portable — no private-skill dependency): short & concise · keyword-rich · bullets for every experience role · benefits over features · active voice · metrics where possible · cut weak intensifiers ("really/very/just") and AI-filler ("I hope this finds you", "leverage", "synergy").

- [ ] **Step 2: Write the Headline playbook** — 220-char limit, formula `[Role] | [Key Expertise] | [Value Prop]`, weak vs strong examples (adapt from optimizer, MIT), and per-mode variants (job/keyword-dense; creator/value-prop; consulting/outcome-led; branding/POV).

- [ ] **Step 3: Write the About playbook** — 2,600-char limit, ~1,500-2,000 recommended, structure (hook → who → achievements → what you offer/seek → CTA), first-~300-char "see more" rule, one full worked example per mode.

- [ ] **Step 4: Write the Experience playbook** — per role: title/company/dates + 2-3 line summary + 4-6 **short bullet** achievements with metrics + keywords. Provide a Current → Optimized before/after example showing weak prose converted to tight bullets.

- [ ] **Step 5: Write the Skills + Keywords playbook** — up to 50 skills, top-3 pinned, keyword sourcing (target job descriptions, peer profiles), placement (headline > About > experience > skills).

- [ ] **Step 6: Commit**

```bash
git add references/copy-playbooks.md
git commit -m "feat: add copy playbooks with embedded portable copy rules"
```

---

### Task 7: Write `references/industry-best-practices.md`

**Files:**
- Create: `linkedin-profile-perfector/references/industry-best-practices.md`

- [ ] **Step 1: Write the best-of-industry checklist** — All-Star completeness requirements, recruiter visibility / Open-to-Work settings, Featured section use, recommendations strategy (5-10, give-first), content cadence (1-5x/week), profile-as-landing-page principles.
- [ ] **Step 2: Write per-mode "what great looks like"** — one short exemplar profile pattern per mode (branding, job, creator, consulting).
- [ ] **Step 3: Commit**

```bash
git add references/industry-best-practices.md
git commit -m "feat: add curated industry best-practices layer"
```

---

### Task 8: Write `assets/report-template.md`

**Files:**
- Create: `linkedin-profile-perfector/assets/report-template.md`

- [ ] **Step 1: Write the markdown template** with `{{PLACEHOLDER}}` tokens and these sections: header (name, goal mode, date), Executive Summary + overall score, Classification (industry/type/audience), Section Scorecard (table: section · score/10 · status · top fix), Rewrites (Headline Current→Optimized; About Current→Optimized; Experience per-role Current→Optimized; Skills), Keywords integrated, Action Checklist (mode-specific), and a "Not assessable from PDF" notice block. Use `{{HEADLINE_OPTIMIZED}}` etc. so render is a token-fill.
- [ ] **Step 2: Commit**

```bash
git add assets/report-template.md
git commit -m "feat: add markdown report template"
```

---

### Task 9: Write `assets/report-template.html`

**Files:**
- Create: `linkedin-profile-perfector/assets/report-template.html`

- [ ] **Step 1: Write a self-contained styled HTML** (inline `<style>`, no external deps, system font stack) mirroring the markdown template's sections, with the same `{{PLACEHOLDER}}` tokens. Include: score badges (color by band: green ≥8, amber 5-7, red <5), Current/Optimized two-column cards, copy-friendly `<pre>` blocks for the optimized copy, print-friendly CSS. Must open standalone in a browser.
- [ ] **Step 2: Verify** it renders by opening in a browser:

```bash
open assets/report-template.html
```
Expected: styled page with placeholder tokens visible, no broken layout.

- [ ] **Step 3: Commit**

```bash
git add assets/report-template.html
git commit -m "feat: add styled standalone HTML report template"
```

---

### Task 10: Write `README.md`

**Files:**
- Create: `linkedin-profile-perfector/README.md`

- [ ] **Step 1: Write README** with: one-line pitch, the 4 goal modes, an input→output diagram, **Install** section (`npx skills add EricTechPro/linkedin-profile-perfector` — confirm exact slug after publish), **Run in Claude Code** section (type the trigger, answer the 7 guided questions, get report.md + report.html), a sample screenshot/section placeholder, honest-limits pointer, License + attribution links.
- [ ] **Step 2: Commit**

```bash
git add README.md
git commit -m "docs: add README (install + run in Claude Code)"
```

---

### Task 11: Write `INSTALL.md`

**Files:**
- Create: `linkedin-profile-perfector/INSTALL.md`

- [ ] **Step 1: Write INSTALL** with: prerequisites (Claude Code), install command, where the skill lands, how to export your LinkedIn PDF (More ▾ → Save to PDF; or Settings → Get a copy of your data), how to provide alternatives (resume/URL/text), and an **Honest Limits** section: no live metrics from a PDF (SSI/views/impressions flagged not faked), no auto-posting, no image generation, advice is best-practice not a guarantee.
- [ ] **Step 2: Commit**

```bash
git add INSTALL.md
git commit -m "docs: add INSTALL.md with honest-limits section"
```

---

### Task 12: Write `ATTRIBUTION.md`

**Files:**
- Create: `linkedin-profile-perfector/ATTRIBUTION.md`

- [ ] **Step 1: Write attribution** crediting `paramchoudhary/resumeskills@linkedin-profile-optimizer` (MIT) for copy playbooks and `schwepps/skills@linkedin-personal-branding` (MIT) for scoring/benchmark/templates, with repo links and SPDX `MIT`. State recruiter-lens + industry-best-practices layers are original. Note Chrome dependency was removed.
- [ ] **Step 2: Commit**

```bash
git add ATTRIBUTION.md
git commit -m "docs: add ATTRIBUTION for MIT upstream sources"
```

---

### Task 13: End-to-end dry run + publish

**Files:**
- Create: `linkedin-profile-perfector/sample-runs/README.md` (note on what sample output demonstrates)

- [ ] **Step 1: Dry-run the skill** in Claude Code against a sample/dummy LinkedIn PDF (or pasted sample profile text) for **Mode 2 (Job search)**. Verify: progress bar renders, dropdowns appear, Steps 3-4 auto-fill from material, report.md + report.html are produced with all sections filled and **zero `{{...}}` tokens remaining**.

```bash
grep -R "{{" linkedin-profile-perfector/runs/*/report.* ; echo "exit=$? (1 = no tokens left = good)"
```
Expected: no matches (exit 1).

- [ ] **Step 2: Repeat for the other 3 modes** (branding, creator, consulting); confirm the emphasis differs per the mode table (headline/About/checklist visibly change).

- [ ] **Step 3: Fix any gaps** found, committing fixes atomically.

- [ ] **Step 4: Create the public GitHub repo and push** (only after user confirms publishing):

```bash
cd /Users/erictech/Desktop/EricTechOS/linkedin-profile-perfector
gh repo create EricTechPro/linkedin-profile-perfector --public --source=. --remote=origin --description "Claude Code skill: turn your LinkedIn material into a recruiter-grade audit + copy-paste rewrites + action plan. 4 goal modes."
git push -u origin main
```

- [ ] **Step 5: Confirm install path** — verify `npx skills add EricTechPro/linkedin-profile-perfector` resolves; update README's install slug if the resolved package id differs.

- [ ] **Step 6: Commit any README slug fix**

```bash
git add README.md && git commit -m "docs: pin verified skills install slug" && git push
```

---

## Self-Review

**Spec coverage:** §1 summary → Tasks 2-12. §3 four modes → Tasks 2,4,6 (mode tables). §4 adaptive intake + progress bar → Task 3. §5 repo structure → Tasks 1-12. §6 embedded copy rules → Task 6 Step 1. §7 output → Tasks 8-9. §9 testing → Task 13. §10 attribution → Task 12. §11 out-of-scope honored (no scripts/scraping/posting/images). Gap check: none outstanding.

**Placeholder scan:** content-authoring tasks specify exact sections + source material + examples to include (not "write appropriate content"). Template tasks define exact `{{TOKEN}}` set. Task 13 asserts zero leftover tokens. No "TBD/TODO".

**Type consistency:** placeholder token names (`{{HEADLINE_OPTIMIZED}}`, etc.) are defined in Task 8 and reused in Task 9; both report files share one token set; Task 13 greps `{{` to enforce. Mode names (Personal branding / Job search / Creator / Consulting) consistent across Tasks 2,3,4,6,7,13.
