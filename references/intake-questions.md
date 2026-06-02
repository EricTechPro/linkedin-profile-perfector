# Adaptive 7-Step Intake — Reference Spec

> **Principle:** context-first, then confirm — not interrogate.
> Gather everything once (Step 2), parse it, auto-fill/skip any later question
> the material answers, and present remaining questions as multiple-choice
> dropdowns with options generated from parsed context plus a
> "🤖 Recommend for me" option and an "Other" free-text escape on each.

---

## Progress Bar — Canonical Format

Always render the progress bar at the top of every intake step using this exact structure:

```
🟩🟩🟩⏭️🟩⬜⬜   Step 5 of 7
✅ 1. Goal — Job search
✅ 2. Material — PDF + website + resume
✅ 3. Target role — Senior PM  (read from your profile)
⏭️ 4. Industry — B2B SaaS  (auto-filled, skipped)
▶️ 5. Audience …
⬜ 6. Achievements …
⬜ 7. Keywords …
```

**Legend:**
| Symbol | Meaning |
|--------|---------|
| 🟩 | Answered by the user |
| ▶️ | Current step — waiting for input |
| ⏭️ | Auto-filled or skipped (one-line reason shown inline) |
| ⬜ | Pending — not yet reached |
| ✅ | (In the detail list) Completed with value shown |

Rules:
- The icon row (top line) mirrors the same order as the detail list.
- For ⏭️ steps, show the auto-filled value and a parenthetical reason, e.g. `(auto-filled, skipped)`.
- Never remove a step from the list; mark it ⏭️ with the reason instead.

---

## The 7 Steps

### Step 1 — 🎯 Goal Mode

**Always asked. Never skipped.**

**Question text:**
> "What's the #1 thing you want this profile to do?"

**How options are generated:**
Fixed set (not context-dependent at this stage — material hasn't been ingested yet).

| Option | Description |
|--------|-------------|
| Personal brand (authority) | Build visibility as a thought leader or domain expert |
| Land a job | Optimize to attract recruiters and pass ATS |
| Grow as creator | Position for followers, collaborators, and monetization |
| Win consulting clients | Convert visitors into inbound leads or referrals |
| 🤖 Help me decide | Agent recommends based on what the user describes in a follow-up |
| Other | Free-text — user states their own goal |

**Effect:** Sets the `mode` variable (`brand` / `job` / `creator` / `consulting` / `other`) used to tune every subsequent step's question framing, option generation, scoring weights, and rewrite priorities.

**Skip rule:** Never skip. This is the mode anchor for the entire audit.

---

### Step 2 — 📥 Material

**Always asked. Never skipped.**

**Question text:**
> "Drop everything you've got — in one place."
> "Select all that apply, then I'll ask for each path, URL, or paste."

**How options are generated:**
Fixed multi-select set.

| Option | What to collect after selection |
|--------|--------------------------------|
| LinkedIn PDF export (file path) | Prompt: "Paste the file path or drag the PDF here." |
| Resume / CV (file path) | Prompt: "Paste the file path or drag the file here." |
| Website URL | Prompt: "Enter the URL (e.g. https://yoursite.com)." |
| Portfolio / project links | Prompt: "List URLs, one per line." |
| Pasted profile text | Prompt: "Paste your LinkedIn profile sections as plain text." |
| Personal notes / wiki | Prompt: "Paste your notes or a link to a doc." |
| Just describe me | Prompt: "Tell me about yourself in your own words. I'll extract what I need." |
| 🤖 Recommend what to gather | Agent explains which sources yield the richest audit and what to grab first. |

**After collection:**
Immediately ingest all provided material before presenting Step 3. Parsing targets:
- Current title, past titles, companies, employment dates
- Industries, sectors, keywords, certifications
- Metrics, achievements, quantified results
- Stated goals or summary language

This parsed context is the source for auto-fill decisions in Steps 3–7.

**Skip rule:** Never skip. Without material, subsequent steps have no context to auto-fill from.

---

### Step 3 — 🧑‍💼 Target Role / Niche

**Auto-fill eligible.**

**Question framing (mode-dependent):**

| Mode | Question text |
|------|--------------|
| `job` | "What title do you want to land?" |
| `consulting` | "What service or role do you offer?" |
| `brand` | "What do you want to be known for?" |
| `creator` | "What do you want to be known for?" |

**How options are generated:**
Extracted from parsed material:
- Current title (from LinkedIn PDF or resume)
- Titles listed in experience section
- Any explicit goal statements ("looking for", "seeking", "passionate about")
- Common progressions inferred from seniority + industry

Always append:
- 🤖 "Use what's already in my profile" (agent picks the strongest match)
- Other (free-text)

**Auto-fill & skip rule:**
If parsed material reveals the target role with **high confidence** (e.g., current title is unambiguous, or a job-search summary explicitly states a target), pre-fill and mark ⏭️ with a one-line reason:
> `⏭️ 3. Target role — Senior PM  (read from your profile headline)`

If confidence is **low** (multiple possible directions, no summary statement), ask with the generated options.

---

### Step 4 — 🏢 Industry / Field

**Auto-fill eligible.**

**Question text:**
> "What industry or field is that in?"

**How options are generated:**
Extracted from parsed material:
- Employer industries (from LinkedIn company names or resume)
- Sector keywords present in the About or Experience sections
- Industry-specific certifications or jargon detected

Always append:
- 🤖 "Match what's in my profile" (agent picks the dominant industry signal)
- Other (free-text)

**Auto-fill & skip rule:**
If parsed material clearly shows a consistent industry (e.g., all experience at B2B SaaS companies, or "fintech" appears multiple times), skip and mark ⏭️:
> `⏭️ 4. Industry — B2B SaaS  (auto-filled, skipped)`

If the user has cross-industry experience without a clear dominant, ask.

---

### Step 5 — 👥 Target Audience

**Cannot auto-fill; always asked.** (User intent for *who to impress* is rarely explicit in a profile.)

**Question text:**
> "Who most needs to be impressed when they land on your profile?"

**How options are generated:**
Options tuned to `mode` + parsed `industry`:

| Mode | Generated option set |
|------|---------------------|
| `brand` | Business owners in my space · Peers / fellow practitioners · Journalists / media · Conference organizers · Investors · 🤖 + Other |
| `job` | Recruiters & talent sourcers · Hiring managers (direct team leads) · Department / VP-level decision makers · Peers who can refer me · 🤖 + Other |
| `creator` | My niche audience (followers / readers) · Potential collaborators · Brand sponsors · Newsletter / media partners · 🤖 + Other |
| `consulting` | Potential clients (decision-makers) · Procurement / procurement committees · Referral partners · 🤖 + Other |

Always append: 🤖 "Recommend the best target for my goal" · Other (free-text).

**Skip rule:** Never skip. The target audience determines tone, keyword priority, and rewrite framing.

---

### Step 6 — ⭐ Top Achievements / Selling Points

**Auto-fill eligible (pre-selection, not skip).**

**Question text:**
> "What are your top 2–3 achievements or biggest selling points?"

**How options are generated:**
Extract achievement candidates from parsed material:
- Quantified results (revenue, growth %, team size, cost savings, shipped products)
- Awards, promotions, notable projects
- Certifications or credentials that signal expertise
- Self-described strengths from summary/About text

Present extracted candidates as **pre-selected checkboxes** — user confirms, deselects, or adds.

Always append:
- 🤖 "Pick my strongest from the material" (agent selects top 2–3 by impact + specificity)
- Other (free-text to add unlisted items)

**Skip rule:**
Never fully skip — even when good candidates are extracted, confirmation from the user is required because achievement framing is subjective. But the extracted pre-selections drastically reduce effort.

---

### Step 7 — 🔑 Keywords to Rank For

**Auto-fill eligible (pre-selection).**

**Question text:**
> "What keywords should your profile rank for in LinkedIn search?"

**How options are generated:**
Auto-generate from the combination of:
- `goal` (mode) + `target_role` + `industry` (Steps 1, 3, 4)
- Keywords already present in the material (high-frequency, high-signal terms)
- Copy-playbook keyword rules: role-exact terms, industry-standard titles, function + level combos, tool/skill names

Present auto-generated keywords as **pre-selected chips** — user confirms, removes, or adds.

Special prompt appended:
> "Paste a target job description to sharpen these keywords further."
> (If pasted, re-run keyword extraction against the JD and merge/re-rank.)

Always append:
- 🤖 "Generate the best keyword set for my goal" (agent generates fresh without pre-selection)
- Other (free-text to add unlisted terms)

**Skip rule:**
Never fully skip — keyword confirmation from the user ensures accuracy. Pre-selection from material makes it fast.

---

## Auto-Fill / Skip Algorithm

### Overview

After ingesting all Step 2 material, the agent runs a parsing pass to attempt to answer Steps 3–7 before presenting each one. The goal is to present only the questions the material cannot answer confidently.

### Confidence Levels

| Level | Definition | Action |
|-------|-----------|--------|
| **High** | Single unambiguous answer extracted; consistent across multiple material sources | Skip (mark ⏭️) or show as a confirmed pre-fill requiring one-tap acceptance |
| **Medium** | Likely answer extracted, but some ambiguity remains | Show with the extracted value pre-selected; user confirms or overrides |
| **Low** | Multiple competing answers, contradictory signals, or nothing found | Present as an open question with generated options |

### Per-Step Rules

| Step | Auto-fill eligible? | Skip trigger |
|------|--------------------|----|
| 1. Goal | No | — |
| 2. Material | No | — |
| 3. Target role | Yes | High-confidence title found (consistent across resume + LinkedIn headline + summary) |
| 4. Industry | Yes | High-confidence industry found (3+ signals in material all point to same sector) |
| 5. Target audience | No | Audience intent is rarely stated explicitly; always ask |
| 6. Achievements | Partial | Never fully skip; pre-select candidates from material, require user confirmation |
| 7. Keywords | Partial | Never fully skip; pre-select from material+goal, require user confirmation |

### Conflict Resolution

If resume and LinkedIn PDF disagree (e.g., different current titles), default to the LinkedIn PDF as primary source (it's the live profile) and surface the discrepancy:
> "Your LinkedIn title says X but your resume says Y — which should we optimize for?"

### Never Skip Steps 1 or 2

These are the foundation of every subsequent decision. Skipping either produces an audit with no grounding and no mode calibration.

---

## Intake Completion

After Step 7 is confirmed, output a structured intake summary for user review before proceeding to the audit:

```
📋 Intake complete — here's what I'll work with:

1. Goal:          Land a job
2. Material:      LinkedIn PDF + resume + LinkedIn URL
3. Target role:   Senior Product Manager
4. Industry:      B2B SaaS
5. Audience:      Recruiters & hiring managers
6. Achievements:  Led $2M revenue feature · Grew team from 4→12 · 40% churn reduction
7. Keywords:      Product Manager · Senior PM · B2B SaaS · Roadmap · Agile · OKRs

Ready to audit? → Yes / Edit an answer
```

The user approves or edits before the audit phase begins. This is the final gate before scoring.
