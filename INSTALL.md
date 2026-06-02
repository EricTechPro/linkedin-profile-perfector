# INSTALL.md

Step-by-step setup for `linkedin-profile-perfector`, including how to get your LinkedIn data and what the skill can and cannot do.

---

## Prerequisites

- **[Claude Code](https://claude.ai/code)** *or* **Claude Cowork** — the host that runs the skill.
- For the Claude Code install path: Node.js ≥ 18 (for `npx`).
- That's it — no LinkedIn account access, no API keys, no browser extensions required.

---

## Install

### Claude Code

```bash
npx skills add EricTechPro/linkedin-profile-perfector
```

The skill installs into `.claude/skills/linkedin-profile-perfector/` in your current project directory.

### Claude Cowork

Hand the repo URL to your agent and let it install itself into your workspace:

```
Install the LinkedIn Profile Perfector skill into this workspace.
Repo: https://github.com/EricTechPro/linkedin-profile-perfector
```

The skill is pure logic — it reads your material, scores it, and writes `report.md` + `report.html` — so once installed it runs the same in either host.

**Confirm it's available** — in Claude Code or Cowork, ask:

```
what skills are installed?
```

You should see `linkedin-profile-perfector` in the list. If not, re-run the install command from the same directory where you plan to use it.

---

## Get your LinkedIn data

The skill works from any combination of material you provide. Start with whichever is easiest.

### Option A — Export your LinkedIn profile as PDF (recommended)

1. Go to your LinkedIn profile page.
2. Click **"More ▾"** (below your profile photo) → **"Save to PDF"**.
3. LinkedIn generates a PDF of your public profile. Save it somewhere accessible (e.g., your Desktop or project folder).

**Alternative export path:**
1. Go to **Settings & Privacy** → **Data Privacy** → **"Get a copy of your data"**.
2. Select **"Want something in particular?"** → check **Profile** → **Request archive**.
3. LinkedIn emails you a download link (usually within minutes for profile-only). The ZIP contains a CSV; the PDF method above is simpler for this skill.

### Option B — No PDF? Use any of these instead

| Alternative | What to provide |
|---|---|
| Resume / CV | File path to your `.pdf` or `.docx` resume |
| Website URL | Your portfolio, personal site, or company page URL |
| Pasted text | Copy-paste your LinkedIn profile sections directly into the chat |
| Just describe yourself | Type a summary — the skill will work from what you tell it |

You can mix sources (e.g., PDF + website URL + resume). Step 2 of the intake accepts all of them.

---

## Run it

1. **Trigger** — say anything like `"optimize my LinkedIn"`, `"LinkedIn profile review"`, `"improve my headline"`, or `"LinkedIn for job search"`.

2. **Answer 7 guided questions** — each renders as a dropdown with context-aware options plus a `🤖 Recommend for me` choice. Questions that can be inferred from your material are auto-filled and skipped. A progress bar tracks your position:

   ```
   🟩🟩🟩⏭️🟩⬜⬜   Step 5 of 7
   ✅ 1. Goal — Job search
   ✅ 2. Material — PDF + website
   ✅ 3. Target role — Senior PM  (read from your profile)
   ⏭️ 4. Industry — B2B SaaS  (auto-filled, skipped)
   ▶️ 5. Audience …
   ```

   The 7 steps: **Goal mode · Material · Target role · Industry · Target audience · Achievements · Keywords.**

3. **Collect your reports** — two files written to:
   ```
   runs/<YYYY-MM-DD>-<slug>/report.md
   runs/<YYYY-MM-DD>-<slug>/report.html
   ```
   Open `report.html` in any browser for the styled, shareable version.

---

## Honest limits

This skill works entirely from the material you provide. Be aware of these boundaries before you start:

- **No live LinkedIn data.** The skill cannot read SSI score, profile views, post impressions, connection count, or engagement rate from a PDF export. These metrics are flagged in the report as "not available — check at linkedin.com/sales/ssi" rather than estimated or fabricated.

- **No LinkedIn account access.** The skill does not log in, open a browser, or interact with LinkedIn in any way. Everything runs locally from the files and text you provide.

- **No auto-posting.** The skill produces a report with rewritten copy. It does not apply changes to your LinkedIn profile automatically — you copy-paste the rewrites yourself.

- **No image or banner generation.** Profile photo and banner recommendations may appear in the audit notes, but no images are generated or uploaded.

- **Advice, not a guarantee.** Rewrites and recommendations follow industry best practices and the scoring framework built into the skill. They are not a guarantee of profile views, recruiter responses, job offers, or follower growth. Results depend on your industry, competition, and how you apply the recommendations.

- **PDF quality varies.** LinkedIn's PDF export omits some sections (e.g., Featured media, recommendations count, connection count). If a section is missing from your PDF, provide it via another source (paste the text, add a URL) so the skill can include it in the audit.
