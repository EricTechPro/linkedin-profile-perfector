# linkedin-profile-perfector — Design Spec

**Date:** 2026-06-02
**Status:** Approved (design + adaptive intake)
**Owner:** EricTechPro

## 1. Summary

A single, public, open-source Claude Code skill that turns **your profile
material + a few guided questions** into a **recruiter-grade audit +
copy-paste-ready rewrites + a slim action checklist**, delivered as both a clean
markdown report and a styled, shareable HTML page.

Curated fusion of three sources plus a hand-authored "best of industry" layer:

- **`paramchoudhary/resumeskills@linkedin-profile-optimizer`** (MIT) — copy
  execution: headline / About / experience rewrite playbooks, formulas, output format.
- **`schwepps/skills@linkedin-personal-branding`** (MIT) — scoring/benchmark brain:
  0–10 per-section scoring with checklists, industry benchmarks, audit + action
  templates. **Chrome dependency removed** — fed by user-provided material instead.
- **Recruiter "6-second scan" lens** — authored from current industry best practice.
- **Industry best-practices layer** — curated current LinkedIn best practices.

Both upstream sources are MIT — combining + republishing allowed with attribution.

## 2. Audience & distribution

- **Audience:** anyone (open-source product). No person-specific hardcoding.
- **Distribution:** standalone **public GitHub repo** under `EricTechPro`,
  installable via `npx skills add`. NOT inside `yt-all-tools`.
- **Quality bar:** mirror the `instagram-carousel` skill structure
  (`SKILL.md` + `references/` + `assets/` + `scripts/` + `VERSION`).
- **Runs in Claude Code.** Progress bar + dropdowns are Claude Code-native.

## 3. Goal modes (4)

Intake Q1 selects ONE mode. The mode re-weights audit, steers rewrite emphasis,
and shapes the action checklist. The **recruiter lens** is the engine behind Mode 2
and a cross-cutting first-impression check on all modes (NOT a 5th mode).

| Mode | For | Audit emphasis | Rewrite emphasis | Action checklist |
|---|---|---|---|---|
| **1. Personal branding** | Build authority / be known | Positioning, differentiation, About-as-manifesto | Narrative headline + About, point-of-view | Content pillars, signature topics, engagement |
| **2. Job search** | Land a role | Recruiter 6-sec scan, ATS keywords, searchability, Open-to-Work, completeness | Keyword-dense headline, metric-driven bullets | Recruiter visibility, target-role keyword gaps, recommendations |
| **3. Creator / grow profile** | Followers, reach, content | Hook quality, cadence, profile-as-landing-page, follower conversion | Headline = value prop for followers, About w/ CTA | Posting schedule, hook formulas, content calendar |
| **4. Consulting / win clients** | Attract clients/leads | Proof, ROI, services clarity, authority | Outcome/ROI headline, About w/ offer + proof, case-study bullets | Services section, lead magnets, social proof, outreach |

## 4. Adaptive intake (core UX)

**Principle: context-first, then confirm — not interrogate.** Gather everything
once (Step 2), parse it, **auto-fill / skip** any later question the material
already answers, and present remaining questions as **multiple-choice dropdowns
with options generated from the parsed context** — plus a **🤖 "recommend for me /
guided"** option and an **Other** free-text escape on each. The user rarely types
from scratch. Every question builds on prior answers + parsed material.

### The 7 steps

| # | Emoji | Question | Option behavior |
|---|---|---|---|
| 1 | 🎯 | What's the #1 thing this profile should do? | Dropdown: Personal brand · Land a job · Grow as creator · Win consulting clients · 🤖 Help me decide → sets mode. Always asked. |
| 2 | 📥 | Drop everything you've got — in one place. | Multi-select of what you have: LinkedIn PDF · Resume · Website URL · Portfolio/past projects · Profile text · Personal wiki/notes · "Just describe me." Skill ingests ALL of it. Always asked. |
| 3 | 🧑‍💼 | Target role / title / niche (framing adapts to mode) | Options generated from material + 🤖 "use what's in my profile" + Other. **Auto-filled & skipped** if Step 2 makes it obvious. |
| 4 | 🏢 | Industry / field | **Skipped if material already shows it** (confirmed silently). Only asked when unknown. |
| 5 | 👥 | Target audience | Recommended options by mode + context (e.g. branding→business owners/peers; job→recruiters/hiring managers; creator→niche audience; consulting→potential clients) + 🤖 recommend. |
| 6 | ⭐ | Top achievements / selling points | Skill proposes the top ones found in material; user picks/edits + adds own. |
| 7 | 🔑 | Keywords to rank for | **Auto-generated** from goal + role + industry via the source playbooks; user confirms/tweaks. Optional: paste a target job to sharpen. No blank paste prompt. |

### Mode-dependent framing of Q3
- Job search → "what title you want to land"
- Consulting → "the service/role you offer"
- Personal branding / Creator → "what you want to be known for"

### Progress bar (Claude Code-native, handles skips)

Plain unicode + emoji, renders in the Claude Code terminal:
```
🟩🟩🟩⏭️🟩⬜⬜   Step 5 of 7
✅ 1. Goal — Job search
✅ 2. Material — PDF + website + resume
✅ 3. Target role — Senior PM  (read from your profile)
⏭️ 4. Industry — B2B SaaS  (auto-filled, skipped)
▶️ 5. Audience …
```
Skipped/auto-filled steps render with ⏭️ and a one-line "why" so the user sees
what was inferred and from where.

### Source-material parsing (Step 2)
Accepts and merges any of: LinkedIn PDF export, resume (PDF/doc), website URL
(fetch), portfolio/project links, pasted profile text, notes, or a freeform
self-description. Extracts: headline · About · experience · skills · education ·
recommendations · role/title signals · industry signals · achievements · keywords.

### PDF / live-metric gap handling
Live-only metrics (SSI, profile views, post impressions, engagement rate) are not
present in a PDF/material export. The audit marks these *"not available — check at
linkedin.com/sales/ssi"* rather than fabricating them. Everything present is fully
scored.

## 5. Repo structure

```
linkedin-profile-perfector/
├── README.md                         what it does, install (npx skills add), how to run in Claude Code, demo
├── INSTALL.md                        step-by-step setup, requirements, honest-limits section
├── LICENSE                           MIT
├── ATTRIBUTION.md                    credits optimizer + schwepps (both MIT)
├── SKILL.md                          orchestration: intake→parse→classify→audit→rewrite→plan→render
├── VERSION
├── references/
│   ├── intake-questions.md           the 7-step adaptive flow + mode routing + progress-bar spec
│   ├── scoring-framework.md          material-aware 0–10 per-section scoring (adapted from schwepps)
│   ├── benchmarks.md                 industry benchmarks, SSI ranges (context, not faked)
│   ├── copy-playbooks.md             headline/About/experience formulas + EMBEDDED copy rules (portable)
│   └── industry-best-practices.md    curated "best of industry" + recruiter 6-sec scan
├── assets/
│   ├── report-template.md
│   └── report-template.html          styled, shareable
```

## 6. Embedded copy rules (portable, no private-skill dependency)

`copy-playbooks.md` bakes in the rewrite principles so the public repo is
self-contained: short & concise · keyword-rich · bullet points for every
experience role · benefits over features · active voice · metrics where possible ·
cut weak intensifiers and AI-sounding filler. Experience rewrite = short bulleted
achievements with keywords, Current → Optimized per role.

## 7. Output

Per-run report (`report.md` + styled `report.html`), tuned to the goal mode:
- Headline → keyword-rich, mode-appropriate
- About → short hook, scannable, benefits-led, CTA
- Experience (every role) → short, concise, bullets, metrics + keywords, Current → Optimized
- Skills → keyword-aligned to target
- Slim prioritized action checklist (mode-specific)

## 8. Components (isolation/clarity)

- **Material ingestion** — Claude reads PDF/resume via Read tool, URLs via WebFetch,
  pasted text inline; merges into a structured profile model.
- **scoring-framework.md + SKILL.md logic** — input: parsed signals + mode;
  output: per-section 0–10 + gaps. Pure reference.
- **copy-playbooks.md + SKILL.md logic** — input: parsed signals + mode + goal;
  output: rewritten copy. Pure reference.
- **Report rendering** — Claude fills assets/report-template.md and
  assets/report-template.html token-by-token and writes them to the run folder.

## 9. Testing

- Validation is dry runs of the skill against sample material asserting the report
  has all required sections and zero unfilled `{{...}}` tokens; run once per goal mode.
- Adaptive-skip logic → given rich Step-2 material, Q3/Q4 auto-fill and progress
  bar shows ⏭️.
- End-to-end dry run: sample material × each of the 4 modes → 4 distinct reports
  whose emphasis differs per the mode table.

## 10. Licensing & attribution

- Repo license: **MIT**.
- `ATTRIBUTION.md` credits both upstream MIT sources with links and which parts
  were adapted. Recruiter-lens + industry-best-practices layers are original.

## 11. Out of scope (YAGNI)

- No live LinkedIn scraping / browser automation.
- No auto-posting or API writes to LinkedIn.
- No image/banner generation (text + report only).
- No SSI/engagement metric fabrication.
