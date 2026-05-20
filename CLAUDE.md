# CLAUDE.md

Context handoff for future Claude Code instances picking up this project. Read this first before doing anything.

---

## 1. Project overview

- Personal portfolio site for **Maanasa Sivashankar**, service designer.
- Repo: https://github.com/Maanasa02/personal-website
- Production deploy: GitHub Pages, expected URL `https://maanasa02.github.io/personal-website/`.
- Stack: static HTML/CSS/JS. **No build step.** Edit files, open in browser, done.

---

## 2. File structure

Top level (`/Users/jaison/Desktop/maanasa/` on this laptop):

- `index.html` — About page. Hero + **Selected Work** carousel + Exhibitions / Publications / Speaking lists.
- `project-1.html` — Police Hiring Redesign.
- `project-2.html` — San Francisco's Homeless Response (ASTRID).
- `project-3.html` — Penske Truck Leasing.
- `project-detail.html` — leftover generic template, not linked anywhere. Safe to delete on request.
- `css/style.css` — single shared stylesheet, ~1500 lines.
- `js/main.js` — mobile nav toggle + work-carousel arrow scroll.
- `project1-title.jpg`, `project2-title.jpeg`, `project3-title.jpg` — hero images for carousel cards on the About page.
- `README.md` — minimal repo readme.

---

## 3. Design system

Defined in `css/style.css`.

CSS variables in `:root`:
- `--bg`, `--fg`, `--muted`, `--accent`, `--border`, `--card`
- `--max-width`, `--radius`
- `--font-sans`, `--font-serif`

Fonts:
- **Serif:** Georgia (used for `.page-title`, `.page-lede`, body copy on project pages).
- **Sans:** system stack (used for nav, labels, meta).

Key shared classes available to all pages:
- Layout: `.page`, `.container`, `.page-title`, `.page-lede`
- Project body: `.project-content`, `.project-callout` (orange-bordered italic quote)
- Stats: `.stat-row`, `.stat`
- Source quotes: `.doc-excerpt`
- Image placeholders: `.needs-image`, `.needs-image-placeholder`, `.needs-image-label`, `.needs-image-desc`
- Overview block: `.overview-grid`, `.overview-item`, `.overview-label`, `.overview-body`
- Work carousel (About page): `.work-section`, `.work-carousel`, `.work-card`
- Extras (About page): `.extras-section`, `.extras-list`, `.extras-item`

Mobile breakpoints:
- `860px` — general layout collapse.
- `640px` — nav toggle activates.

---

## 4. Page patterns

Every project detail page (`project-1.html`, `project-2.html`, `project-3.html`) follows the same anatomy:

1. Top nav — logo removed, just `About` + `LinkedIn` (external link).
2. back-link `← Back to work` pointing to `index.html#work`.
3. `<h1 class="page-title">` + `<p class="page-lede">` + `<dl class="project-meta">` with **Client / Role / Year**.
4. `<section class="overview-grid">` — three-column **Challenge / Solution / Outcome**. No `border-top`, only `border-bottom`.
5. `<article class="project-content">` — H2/H3 sections sourced from the docx, using `.stat-row`, `.doc-excerpt`, `.project-callout`, and `.needs-image` placeholders as needed.
6. Footer — copyright only.

---

## 5. Content rule: artifacts vs concepts

**This is critical. Read carefully.**

- **Use a real image file** for the designer's actual artifacts: wireframes, prototype UI screens, journey maps, hand-drawn sketches, service blueprints as deliverables, photos of workshops.
- **Recreate in HTML/CSS** for concept content: stat blocks, doc excerpts, structured comparisons, simple diagrams that are conceptual rather than authored.
- **When in doubt, prefer a `.needs-image` placeholder** and ask the user for the artifact.

---

## 6. Workflow rules (HOW THE USER WANTS CLAUDE TO WORK)

These rules override Claude's defaults. Follow them strictly.

- **Always delegate to sub-agents.** Claude never edits files directly on this project. Spawn an Agent with the work, let it verify, report back.
- **Always run sub-agents in background** — every Agent call uses `run_in_background: true` so the user can keep chatting while work happens. Never block on a foreground agent.
- **Verify after every change.** After any file edit, fire a follow-up background agent that captures screenshots of affected pages (desktop + mobile) and reviews for visual regressions.
- **Never run two file-editing agents in parallel.** Serialize them. Wait for completion notifications.
- **Use the screenshot QA tool when available** (see Section 7). On a fresh clone, this tooling is NOT in the repo — it lives in the `screenshots/` directory which is gitignored.

---

## 7. Screenshot QA tooling (THIS LAPTOP ONLY; not in repo)

The `screenshots/` directory contains a puppeteer-based capture script:

- `capture.sh` / `capture.js`
- Usage: `node screenshots/capture.js <path> <desktop|mobile> <output.png>`
- Viewports:
  - desktop: **1280x800**
  - mobile: **390x844**
- Output is auto-chunked: PNGs limited to **1900px tall** to stay under Anthropic's 2000px image-read limit.
- Long pages produce multiple chunks (e.g. `output.png`, `output-2.png`, `output-3.png`, ...).

**This tooling is gitignored.** It will NOT be present after a fresh clone. To reinstate:
```bash
npm init -y
npm install puppeteer-core
# then recreate screenshots/capture.js
```
The script can be re-derived from Claude's chat history if needed — tell the user.

---

## 8. Current state — known TODOs

**Image placeholders on `project-2.html` (9 total):**
- Layer 1 cross-departmental swimlane
- Theory-of-change
- CRIT Team Summary
- Layer 3 SPL acuity (Jane Doe)
- Rapid Access prototype
- Layer 4 Response Team wireframes
- Follow-up Team wireframes
- Paper-prototype photo
- Senior Women pilot timeline

**Image placeholders on `project-1.html` (4):**
- Funnel diagram
- Cross-agency journey map
- Shared hiring dashboard
- Service blueprint

**Image placeholders on `project-3.html` (3):**
- Billing journey map
- Rhythm of Work diagram
- Customer Start-up Visual

**Open content items — project-3:**
- Year is `"2022"`.
- Client is `"Penske Truck Leasing (via Zenda Consulting)"`.
- Lede is agent-written; the user may want different wording.

**Open content items — project-2:**
- Various small content questions flagged in prior sessions (acuity score values, etc.). Check session memory if accessible.

**Cleanup:**
- `project-detail.html` is a dead leftover. Safe to delete on request.

---

## 9. Local development

```bash
cd /path/to/personal-website
python3 -m http.server 8000
# open http://localhost:8000
```

That's it. No build, no watcher, no install.

---

## 10. Git workflow

- **Remote:** `git@github.com:Maanasa02/personal-website.git` (SSH only).
- **Default branch:** `main`.
- **Pre-push hook on this laptop blocks direct pushes to main.** Use feature branches:
  ```bash
  git checkout -b update/<short-description>
  # commit
  git push -u origin update/<short-description>
  # open PR on GitHub, merge there
  ```
- The initial bootstrap commit was pushed with `--no-verify` (one-time exception). Don't do this again.

**SSH config on this laptop:**

The repo's `core.sshCommand` is set to:
```
ssh -i ~/.ssh/id_maanasa -o IdentitiesOnly=yes -o IdentityAgent=none
```

This is because macOS Keychain was silently substituting a different key (`jaisontj`'s) via the Apple ssh-agent. On a new laptop with a fresh SSH setup, this config likely isn't needed — git will use the default key.

**Local git identity for this repo (set via `git config --local`, NOT global):**
```
Maanasa Sivashankar <maan.shiv@gmail.com>
```

---

## 11. What's gitignored

From `.gitignore`:
- `screenshots/` — QA tooling, debug captures, and `Penske.docx` source file.
- `*.docx` — content source files, kept locally.
- `Screenshot 2026-05-19 at *.png` — 15 source screenshots from the user, kept locally.
- `123.png`, `p1-1.png` … `p1-4.png`, `challenge.png` — reference inputs.
- `node_modules/`, `package.json`, `package-lock.json` — dev tooling only (existed for the screenshots/ tooling).
- `.DS_Store`, `*.log`, `.tmp/`, `tmp/`
- `.vscode/`, `.idea/`

---

## 12. Source content files (kept locally only)

- `Cross-Department Homelessness Coordination.docx` — source for project-2 body content.
- `PoliceHiringRedesign.docx` (also referred to as `Police Hiring Redesign.docx`) — source for project-1 body content.
- `screenshots/Penske.docx` — source for project-3 body content.

These docx files are the content sources. When the user edits one, the corresponding HTML page's body should be re-synced — **preserving the header, the `.overview-grid` block, and any `.needs-image` placeholders**.

To extract docx text:
```bash
textutil -convert txt "<file>.docx"
```

---

## 13. Deployment

- GitHub Pages target: `https://maanasa02.github.io/personal-website/`.
- To enable: GitHub repo → **Settings → Pages → Source: `main`, root folder**.
- Future: point a custom domain via DNS.

---

## 14. Quick orientation for a new Claude instance

- Read this CLAUDE.md first.
- The user's preferred workflow is in **Section 6** — follow it strictly (delegate, background, serialize, verify with screenshots).
- If continuing where we left off, the most likely next requests are:
  1. Replacing `.needs-image` placeholders with real images the user provides.
  2. Further content polish on any of the three project pages.
  3. Enabling GitHub Pages.
  4. Custom domain setup.
- Confirm the screenshot QA tool exists (Section 7) before relying on it. If missing, tell the user it can be re-derived from chat history.
