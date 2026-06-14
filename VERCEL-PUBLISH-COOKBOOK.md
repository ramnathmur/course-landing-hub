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

| # | Icon | Title | Status | URL |
|---|------|-------|--------|-----|
| 1 | 🧠 | LLM Intelligence & Agent Autonomy | LIVE NOW | https://llm-intelligence-course.vercel.app |
| 2 | 🤖 | Agents Master Course | LIVE NOW | https://agents-master-course.vercel.app |
| 3 | 📖 | Book-to-HTML Learning | LIVE NOW | https://book-to-html.vercel.app |
| 4 | 🏗️ | AI-First Application Development Blueprint | LIVE NOW | https://ai-first-blueprint.vercel.app |
| 5 | 🎯 | Claude Certified Architect Prep | LIVE NOW | https://claude-certified-architect-prep-two.vercel.app |

---

## 2. NEVER DO LIST

These mistakes have happened before. Do not repeat them.

1. **Never copy `AI Course Hub.dc.html` or `AI Course Hub - Standalone.html` over `index.html`** — that overwrites the minimalist design with a cyberpunk theme.
2. **Never use `routes` in `vercel.json`** — the catch-all pattern `{"src": "/.*", "dest": "/index.html"}` breaks sub-file serving (support.js, CSS, etc. get redirected to index.html). Always use `rewrites`.
3. **Never edit `index.html` without first reading the live site** — see the QA Protocol in Section 5.
4. **Never hardcode `status-coming` for a newly added live course** — use `status-live` with text "LIVE NOW".
5. **Never deploy without running `vercel --prod`** — staging URLs (`.vercel.app` with project hash) return 401.

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
    <span class="course-status status-live">LIVE NOW</span>
    <h3 class="course-title">[Full Course Title]</h3>
    <p class="course-description">[2–3 sentence description of what the course covers and who it's for.]</p>
    <div class="course-duration">[N modules • N topics • Self-paced]</div>
    <a href="[VERCEL_URL]" class="btn btn-primary course-cta">Start Learning →</a>
  </div>
</div>
```

Use `status-coming` (grey badge) instead of `status-live` (green badge) if the course is not yet deployed.

### Step 4 — Add footer link
In the `<div class="footer-section">` block under `<h3>Courses</h3>`, add:
```html
<a href="[VERCEL_URL]">[Short Course Name]</a>
```

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
- [ ] Status badge is correct (`LIVE NOW` vs `COMING SOON`)
- [ ] Footer COURSES column includes the new link
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

The last validated minimalist commit was `5a4ab9e` (as of 2026-06-14).

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

- **Hero CTA wiring** — "Explore Courses" button scrolls to `#courses` but is a `<button>` not an `<a>` — add `onclick="document.getElementById('courses').scrollIntoView()"` or convert to anchor.
- **Footer placeholder links** — Documentation, FAQ, Blog, Contact, Discord, Twitter, LinkedIn, GitHub, Privacy Policy, Terms, Cookie Policy, Accessibility all link to `#`. Wire when real URLs exist.
- **Course count in hero** — Hero says "5,200+ learners" (hardcoded). Consider making it dynamic or updating when significant milestones pass.
- **Custom domain** — `neural-hub.com` or similar for a branded URL instead of `.vercel.app`.
- **`og:image` meta tags** — Social sharing previews are missing. Add Open Graph tags to `<head>` for better link previews.
