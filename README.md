# 💼 LinkedIn Profile Perfector

Turn your LinkedIn material into a **recruiter-grade audit + copy rewrites** — right inside **Claude Code** or **Claude Cowork**.
Upload your PDF (or paste your profile), answer **7 guided questions**, and get a scored report you can act on in an afternoon.

```text
  ── INTAKE ──                  ── PIPELINE ──                       ── OUTPUT ──

  your material      ┌────────┐                ┌────────────────┐               report.md
  PDF · resume  ───▶ │  7 Qs  │ ──answers────▶ │ parse → audit  │ ──▶  runs/    + report.html
  site · text        │ intake │  (auto-fills   │ → rewrite →    │     <date>/   (scored · shareable)
  "optimize my       └────────┘   what it can)  │   plan        │
   LinkedIn"                                    └────────────────┘
```

## ⚙️ How it works

- **`Trigger`** → say anything like *"optimize my LinkedIn"*, *"LinkedIn profile review"*, or *"LinkedIn for job search"*.
- **`7 guided questions`** → each renders as a dropdown with context-aware options, a **🤖 Recommend for me** choice, and a progress bar. Anything inferable from your material is auto-filled and skipped (⏭️).
- **`Two reports`** → `report.md` (plain, versionable) + `report.html` (styled, shareable): a scored audit, Current → Optimized rewrites for every section, and a ≤10-item action checklist. Nothing touches your live profile — you copy what you want.

## 🎯 Goal modes — pick one, it re-weights everything

| Mode | What it optimizes for |
|---|---|
| 🏆 **Personal branding** | Authority positioning, About-as-manifesto, thought-leader framing |
| 🔍 **Job search** | Recruiter 6-second scan, ATS keywords, searchability, completeness |
| 📈 **Creator / grow profile** | Hook quality, profile-as-landing-page, follower conversion |
| 🤝 **Consulting / win clients** | Proof, ROI, services clarity, client-attraction copy |

## ✅ Prerequisites

- **Claude Code** *or* **Claude Cowork** — the host that runs the skill.
- **Your LinkedIn as a PDF** *(recommended)* — a 2-click export; a resume, website URL, or pasted text all work too.
- **No API keys, no account access** — it works entirely from the material you hand it.

👉 PDF-export steps and the full prereq list → **[INSTALL.md](INSTALL.md)**.

## 📦 Install

Paste the block for your host — your agent installs the skill for you:

**Claude Code**

```
Install the LinkedIn Profile Perfector skill into this project.
Repo: https://github.com/EricTechPro/linkedin-profile-perfector
Run: npx skills add EricTechPro/linkedin-profile-perfector
```

**Claude Cowork**

```
Install the LinkedIn Profile Perfector skill into this workspace.
Repo: https://github.com/EricTechPro/linkedin-profile-perfector
```

Prefer the terminal? In Claude Code, just run `npx skills add EricTechPro/linkedin-profile-perfector` directly. Prereqs + how to confirm → **[INSTALL.md](INSTALL.md)**.

## ▶️ Run

Trigger it in plain language, then answer the dropdowns:

```
optimize my LinkedIn
LinkedIn profile review
LinkedIn for job search
improve my LinkedIn headline
```

A progress bar tracks the 7 steps and shows what got auto-filled:

```
🟩🟩⬜⬜⬜⬜⬜   Step 2 of 7
✅ 1. Goal — Job search
▶️ 2. Material — Drop everything you've got …
```

## 📋 What you get

- **Scored section audit** — every section graded 0–10 with gap notes + a recruiter 6-second-scan (the CLICK-vs-SKIP test).
- **Current → Optimized rewrites** — headline · About · every experience role · skills — copy-paste ready.
- **Before → projected score** — the Optimized copy is re-scored on the same rubric, so you see the lift each rewrite buys.
- **Target keywords** — role- and industry-specific terms, with a gap table showing where each appears (or doesn't).
- **Slim action checklist** — ≤10 fixes, ordered by impact for your goal mode.

## ⚠️ Honest limits

It works only from the material you provide — so it **cannot**:

- Read live metrics (SSI, profile views, post impressions) from a PDF — those are flagged *"check at linkedin.com/sales/ssi"*, never guessed.
- Auto-post, edit your profile, or access your account in any way.
- Generate profile photos or banner graphics.
- Guarantee outcomes — it follows industry best practices, not a promise of views or hires.

## 📁 What gets created

```text
runs/<YYYY-MM-DD>-<slug>/
├── report.md      # plain, versionable
└── report.html    # styled, browser-ready — open it to share
```

## 📐 License & attribution

MIT — see [LICENSE](LICENSE). Built by fusing two MIT upstream sources (copy playbooks + scoring framework) plus an original recruiter-lens and industry-best-practices layer. Full credit → **[ATTRIBUTION.md](ATTRIBUTION.md)**.
