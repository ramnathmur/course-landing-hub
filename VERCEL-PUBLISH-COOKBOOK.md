# VERCEL PUBLISH COOKBOOK
**Course Landing Hub — Canonical Publishing Reference**

> **TRIGGER PHRASES** — When any of these appear in a conversation in this project context, read this file before acting:
> - "add course to hub"
> - "add a card to the landing page"
> - "publish to Vercel"
> - "deploy course"
> - "update landing hub"
> - "new course on the hub"
> - "Vercel deployment"
> - "landing page update"

---

## 1. SITE INVENTORY (DO NOT ALTER)

### Live URLs
| Resource | URL |
|----------|-----|
| **Landing Hub** | https://course-landing-hub.vercel.app |
| GitHub Repo | https://github.com/ramnathmur/course-landing-hub |
| Local Project | `C:\Claude Cowork\Projects\course-landing-hub\` |
| **Topic Briefer collection** | https://topic-briefer.vercel.app |
| Topic Briefer GitHub | https://github.com/ramnathmur/topic-briefer |
| Topic Briefer Local | `C:\Claude Cowork\Projects\topic-briefer\` |

### Design Identity — LOCKED
This site uses a **minimalist light-mode design**. Never replace it with a cyberpunk, dark, or Claude Design dc.html theme.

| Token | Value | Usage |
|-------|-------|-------|
| `--bg-primary` | `#f9fafb` | Page background |
| `--bg-secondary` | `#f3f4f6` | Card icon area, secondary bg |
| `--text-primary` | `#1f2937` | Headings, body |
| `--text-secondary` | `#374151` | Descriptions |
| `--text-muted` | `#6b7280` | Metadata, duration |
| `--border` | `#e5e7eb` | Card borders, dividers |
| `--accent-warm` | `#f59e0b` | **Primary CTA buttons (amber)** |
| `--accent-teal` | `#14b8a6` | Secondary elements |
| `--accent-cyan` | `#06b6d4` | Tertiary accents |

Font: system font stack (`-apple-system, BlinkMacSystemFont, 'Segoe UI', ...`)  
Layout: sticky header (64px) → hero → courses grid (auto-fit, min 300px) → dark footer  
Card style: white bg, `1px solid var(--border)`, 8px radius, lift on hover

### Course Inventory (as of 2026-06-14)

Badges are now **differentiated** — do not default all cards to "LIVE NOW":

| # | Icon | Title | Badge | CSS Class | URL |
|---|------|-------|-------|-----------|-----|
| 1 | 🧠 | LLM Intelligence & Agent Autonomy | START HERE | `status-featured` | https://llm-intelligence-course.vercel.app |
| 2 | 🤖 | Agents Master Course | LIVE | `status-live` | https://agents-master-course.vercel.app |
| 3 | 📖 | Book-to-HTML Learning | LIVE | `status-live` | https://book-to-html.vercel.app |
| 4 | 🏗️ | AI-First Application Development Blueprint | LIVE | `status-live` | https://ai-first-blueprint.vercel.app |
| 5 | 🎯 | Claude Certified Architect Prep | NEW | `status-new` | https://claude-certified-architect-prep-two.vercel.app |
| 6 | 📄 | Topic Briefer — Deep Dive AI Briefs | NEW | `status-new` | https://topic-briefer.vercel.app |

**Badge CSS classes available:**
- `status-featured` (amber — `#fef3c7` / `#92400e`) → use for the flagship / recommended-first course
- `status-live` (green — `#dcfce7` / `#15803d`) → established courses
- `status-new` (blue — `#dbeafe` / `#1e40af`) → recently added
- `status-coming` (grey — `#f3f4f6` / `#4b5563`) → not yet deployed

---

## 2. NEVER DO LIST

These mistakes have happened before. Do not repeat them.

1. **Never copy `AI Course Hub.dc.html` or `AI Course Hub - Standalone.html` over `index.html`** — that overwrites the minimalist design with a cyberpunk theme.
2. **Never use `routes` in `vercel.json`** — the catch-all pattern `{"src": "/.*", "dest": "/index.html"}` breaks sub-file serving (support.js, CSS, etc. get redirected to index.html). Always use `rewrites`.
3. **Never edit `index.html` without first reading the live site** — see the QA Protocol in Section 5.
4. **Never default all badges to "LIVE NOW"** — use the badge system in the Course Inventory table (Section 1). New courses get `status-new NEW`; the flagship stays `status-featured START HERE`; established ones use `status-live LIVE`.
5. **Never deploy without running `vercel --prod`** — staging URLs (`.vercel.app` with project hash) return 401.
6. **Never skip the dedup check** — always run `Test-Path` (topic-briefer) or `Select-String` (landing hub) before adding a file or card. See Section 4 Step 0 and Section 11 for the one-liners.
7. **Never use `vercel project rm` while background deploys are running** — removing the project kills in-flight deployments and causes "Deployment not found" errors. Always wait for or cancel active deploys first.

---

## 3. CANONICAL `vercel.json`

All projects in this ecosystem use this exact config. Copy it verbatim — do not add `routes`:

```json
{
  "buildCommand": "true",
  "outputDirectory": ".",
  "rewrites": [
    {
      "source": "/(.*)",
      "destination": "/$1"
    }
  ]
}
```

---

## 4. ADD A NEW COURSE CARD

### Step 0 — Deduplication check (run first)
Before touching `index.html`, confirm the course URL is not already linked:

```powershell
$url  = "https://NEW-COURSE-URL.vercel.app"   # ← set this
$file = "C:\Claude Cowork\Projects\course-landing-hub\index.html"
if (Select-String -Path $file -Pattern ([regex]::Escape($url)) -Quiet) {
    Write-Warning "DUPLICATE: $url already appears in index.html — abort."
} else {
    Write-Host "OK: URL not found — safe to add card."
}
```

If the check returns a warning, **stop**. The course is already on the hub. Verify the existing card is correct and re-deploy only if content changed.

### Step 1 — Read the live site first
Before touching `index.html`, verify current state:
```powershell
# Check the live page is healthy
Invoke-WebRequest -Uri "https://course-landing-hub.vercel.app" -UseBasicParsing | Select-Object StatusCode
```
Expected: `StatusCode : 200`. If not 200, investigate before proceeding.

### Step 2 — Read `index.html` locally
Open `C:\Claude Cowork\Projects\course-landing-hub\index.html` and find:
- The last `<!-- Course N: ... -->` comment inside `<div class="courses-grid">`
- The footer `<div class="footer-section">` with the COURSES column

### Step 3 — Insert the new course card
Add after the last `</div>` closing the previous course card, before the closing `</div></section>`:

```html
<!-- Course N: [Title] -->
<div class="course-card">
  <div class="course-icon">[EMOJI]</div>
  <div class="course-content">
    <span class="course-status status-new">NEW</span>
    <h3 class="course-title">[Full Course Title]</h3>
    <p class="course-description">[2–3 sentence description of what the course covers and who it's for.]</p>
    <div class="course-duration">[N modules • N topics • Self-paced]</div>
    <a href="[VERCEL_URL]" class="btn btn-primary course-cta">Start Learning →</a>
  </div>
</div>
```

Choose the badge that fits (see badge table in Section 1):
- Brand-new course → `status-new` / `NEW`
- Established course → `status-live` / `LIVE`
- Not yet deployed → `status-coming` / `COMING SOON`
- Promoted to flagship → `status-featured` / `START HERE` (only one card at a time)

### Step 4 — Add footer link
The footer is now a single horizontal nav row (no more column sections). Find `<div class="footer-nav">` and add one `<a>` before the closing `</div>`:
```html
<a href="[VERCEL_URL]">[Short Course Name]</a>
```
The label stays in the same inline row with the other course links — no `<h3>` or section wrapper needed.

### Step 5 — Verify locally (optional but recommended)
Open `index.html` in a browser and confirm the card appears correctly.

### Step 6 — Deploy
```powershell
cd "C:\Claude Cowork\Projects\course-landing-hub"
git add index.html
git commit -m "Add [Course Name] card to landing hub"
git push
vercel --prod --yes
```

### Step 7 — QA the live site
See Section 5 (QA Protocol).

---

## 5. QA PROTOCOL — INSPECT LIVE SITE

After any deployment, verify:

```powershell
# 1. Confirm the page serves (200)
Invoke-WebRequest -Uri "https://course-landing-hub.vercel.app" -UseBasicParsing | Select-Object StatusCode

# 2. Count course cards (should match expected number)
$html = (Invoke-WebRequest -Uri "https://course-landing-hub.vercel.app" -UseBasicParsing).Content
([regex]::Matches($html, 'class="course-card"')).Count

# 3. Confirm the new course URL appears
$html -match "NEW_COURSE_URL"
```

For visual QA, use headless Chrome (if Chrome is available):
```powershell
& "C:\Program Files\Google\Chrome\Application\chrome.exe" `
  --headless --disable-gpu `
  --screenshot="$env:USERPROFILE\Desktop\hub-qa.png" `
  --window-size=1400,3500 `
  --virtual-time-budget=5000 `
  "https://course-landing-hub.vercel.app"
```

**Check list:**
- [ ] All N courses appear (count matches)
- [ ] New course card shows correct title, description, duration
- [ ] CTA button links to the correct Vercel URL
- [ ] Status badge matches the badge table in Section 1 (START HERE / LIVE / NEW / COMING SOON)
- [ ] Footer nav row includes the new course link
- [ ] No orphaned or truncated text visible
- [ ] Design unchanged (light mode, amber buttons, white cards)

---

## 6. DEPLOY A NEW COURSE TO VERCEL

Use this when a course project needs its own Vercel URL. Follow the LLM Intelligence course as the template:
- GitHub: https://github.com/ramnathmur/llm-intelligence-course
- Vercel: https://llm-intelligence-course.vercel.app
- Pattern: `launch.html` gateway → `src/index.html` main course

### Steps

```powershell
# 1. Navigate to the course project folder
cd "C:\Claude Cowork\Projects\[course-folder]"

# 2. Ensure vercel.json exists (copy from canonical above)
# 3. Initialize git if not already
git init
git add .
git commit -m "Initial commit — [Course Name]"

# 4. Create GitHub repo (do not initialize with README — causes merge conflict)
gh repo create ramnathmur/[repo-name] --public

# 5. Push
git remote add origin https://github.com/ramnathmur/[repo-name].git
git push -u origin master

# 6. Deploy to Vercel prod
vercel --prod --yes
```

Note the Vercel URL returned. If Vercel appends `-two` or similar (name already claimed), use the assigned URL — update the hub card and dc.html source accordingly.

---

## 7. ROLLBACK PROCEDURE

If `index.html` is accidentally overwritten:

```powershell
# Find the last known-good commit
cd "C:\Claude Cowork\Projects\course-landing-hub"
git log --oneline -10

# Restore index.html from that commit
git checkout [COMMIT_SHA] -- index.html

# Redeploy
git add index.html
git commit -m "Restore minimalist landing hub design"
git push
vercel --prod --yes
```

The last validated minimalist commit was `5502e73` (UX audit pass — 2026-06-14).

---

## 8. NEURAL HUB dc.html (CYBERPUNK VERSION — NOT THE LIVE SITE)

The Neural Hub was originally built in Claude Design as a cyberpunk theme. That design exists in:
- `C:\Users\ramna\Downloads\design-output\AI Course Hub.dc.html` (editable source, 21KB, requires `support.js`)
- `C:\Users\ramna\Downloads\design-output\AI Course Hub - Standalone.html` (172KB compressed bundle — cannot be directly edited)

**This is NOT the live landing hub.** The live site uses the minimalist design (`index.html`).

To add a course to the dc.html version, use `makeCourse()`:
```javascript
makeCourse({
  id: '06',
  title: 'New Course Title',
  description: '...',
  duration: 'Self-paced · N courses',
  tag: 'NEW',
  link: 'https://course.vercel.app',
  accent: '#hex', r: 0, g: 0, b: 0,
}),
```

The standalone HTML must be regenerated via Claude Design after editing dc.html.

---

## 9. PROJECT FILE MAP

```
C:\Claude Cowork\Projects\course-landing-hub\
├── index.html          ← THE live landing page (minimalist design — DO NOT REPLACE)
├── vercel.json         ← Passthrough rewrites config
├── README.md           ← Public repo README
└── VERCEL-PUBLISH-COOKBOOK.md  ← This file
```

GitHub: https://github.com/ramnathmur/course-landing-hub  
Vercel Project: `course-landing-hub` (under ramnathmur account)

---

## 10. RECOMMENDED ADDITIONS (Future Sessions)

These are not yet implemented — flag them if relevant:

- **Custom domain** — `neural-hub.com` or similar for a branded URL instead of `.vercel.app`.
- **`og:image` meta tags** — Social sharing previews are missing. Add Open Graph tags to `<head>` for better link previews.
- **Instructor identity section** — A short "who built this" block would help trust; skipped for now pending content.

**Completed in prior sessions (do not re-implement):**
- ~~Hero CTA wiring~~ — Converted `<button>` to `<a href="#courses">`, removed unused "Learn More" button.
- ~~Footer placeholder links~~ — All dead `#` links removed; footer stripped to courses row + copyright only.
- ~~Course count in hero~~ — "5,200+ learners" removed from hero paragraph.
- ~~Badge differentiation~~ — All badges now use the 4-tier system (see Section 1), replacing the old blanket "LIVE NOW".

## 11. TOPIC BRIEFER SITE — SEPARATE VERCEL PROJECT

The Topic Briefer is a standalone multi-file collection site, separate from the landing hub.

| Field | Value |
|-------|-------|
| Live URL | https://topic-briefer.vercel.app |
| GitHub | https://github.com/ramnathmur/topic-briefer |
| Local | `C:\Claude Cowork\Projects\topic-briefer\` |

**File inventory:**
- `index.html` — dark-theme index listing all 14 briefs in two sections
- 5 briefs from `C:\Claude Cowork\CLAUDE OUTPUTS\Topic Briefer\`
- 9 briefs from `C:\Claude Cowork\Projects\topic-explainer\output\` (Topic Explainer v1.2 series)

**To add a new brief — deduplication check first:**

```powershell
# Step 1 — Check before copying (run for every new file)
$src  = "C:\Claude Cowork\CLAUDE OUTPUTS\Topic Briefer\new-brief.html"  # ← set this
$dest = "C:\Claude Cowork\Projects\topic-briefer"
$name = Split-Path $src -Leaf
if (Test-Path "$dest\$name") {
    Write-Warning "DUPLICATE: '$name' already exists in topic-briefer — skipping."
} else {
    Copy-Item $src $dest
    Write-Host "OK: '$name' copied — proceed to update index.html."
}
```

If the check returns a warning, the file is already deployed. Do not re-copy. If content genuinely changed, use `Copy-Item $src $dest -Force` deliberately.

```powershell
# Step 2 — Commit and deploy (only after check passes)
cd "C:\Claude Cowork\Projects\topic-briefer"
git add .
git commit -m "Add [brief title]"
git push
vercel --prod --yes
```

**Known issue:** If `vercel --prod --yes` hangs at "Building…" with UNKNOWN status, the project may have a stale GitHub link. Fix: delete the project (`echo "y" | vercel project rm topic-briefer`), remove the `.vercel` folder, then redeploy fresh.
