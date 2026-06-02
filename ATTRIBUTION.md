# ATTRIBUTION.md

`linkedin-profile-perfector` was built by fusing two MIT-licensed upstream sources with an original layer. Full credit below.

---

## Upstream source 1 — paramchoudhary/ResumeSkills (linkedin-profile-optimizer)

**Repository:** https://github.com/Paramchoudhary/ResumeSkills
**License:** MIT
**SPDX identifier:** `MIT`

**What was adapted from this source:**

- Headline writing formulas (role + differentiator + outcome structure).
- About / Summary section copy playbook (hook → proof → CTA arc).
- Experience description formulas (action verb + metric + impact pattern).
- Output format: the `Current → Optimized` rewrite pair presentation used throughout the report.
- General report structure: section-by-section review with actionable rewrites.

---

## Upstream source 2 — schwepps/skills (linkedin-personal-branding)

**Repository:** https://github.com/schwepps/skills
**License:** MIT
**SPDX identifier:** `MIT`

**What was adapted from this source:**

- Scoring framework: 0–10 per-section scoring rubric and weighting logic.
- Industry benchmarks: baseline scores and gap thresholds by industry and role type.
- Audit template structure: section-by-section gap identification and priority flagging.
- Action checklist template: slim prioritized checklist format (≤10 items, ordered by impact).

**What was removed:**

The original schwepps skill included a live-browser (Chrome) dependency that read metrics directly from a logged-in LinkedIn session. That dependency has been **entirely removed**. This skill works exclusively from user-provided material (PDF, resume, website URL, or pasted text) — it does not open a browser or access any LinkedIn account.

---

## Original contributions (not from either upstream source)

The following elements were created for this skill and are not derived from either upstream:

- **Recruiter 6-second-scan lens** — a cross-mode analysis pass that evaluates the profile from a recruiter's rapid-scan perspective, applied regardless of goal mode.
- **Industry-best-practices layer** — curated guidance specific to industries and role types, layered on top of the scoring framework to produce contextually accurate gap notes and rewrites.
- **Four goal modes** — the Personal branding / Job search / Creator / Consulting framework that re-weights the entire pipeline based on the user's declared objective.
- **7-step adaptive intake** — the progress-bar-driven, dropdown-rendered question flow with auto-fill logic.
- **HTML report template** — the styled standalone `report.html` output format.

---

## License

This project is licensed under the **MIT License**. See [LICENSE](LICENSE).

All upstream material incorporated here was also published under MIT. No GPL, LGPL, or copyleft-licensed material is included.
