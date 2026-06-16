---
name: ci-kit-workflow
description: >
  Full workflow for brand/CI research (Mode 1), HTML overview pages (Mode 3),
  style guide generation (Mode 4), SFTP deployment, and post-build auditing.
  Covers template adaptation, CSS separation, multi-file font consistency,
  dark mode architecture, and deployment safety patterns.
version: 3.0.0
tags: [ci-kit, brand, design, html, sftp, web, clients, dev, designsystem]
---

# CI-Kit Workflow

A complete agency workflow for researching client brand identities, building
HTML overview pages, generating style guides, and deploying to a web server.

---

## Workflow Overview (8 Steps)

1. **Receive URL** — Client sends their website URL
2. **CI Research** — Scrape website, imprint, social media → `clients/PROJECTNAME/docs/ci-kit.md`
3. **Email report** — Send summary with HTML signature
4. **Build overview page** — Fill template with CI-Kit data, adjust CSS tokens
5. **Post-build audit** — Check for template leftovers + completeness
6. **SFTP upload** — `/web/PROJECTNAME/index.html` + entry in `/web/index.html`
7. **Share link** — Send live URL to client
8. **Review together** — Feedback, revisions, iterations

---

## Project File Structure

```
workspace/clients/
└── clientname/
    ├── SOUL.md                ← Agent entry point (start here)
    ├── README.md              ← Quick overview
    ├── index.html             ← Agency project overview page
    ├── docs/
    │   ├── DESIGN.md          ← Design system (YAML + Markdown)
    │   ├── ci-kit.md          ← Brand research output
    │   ├── tokens.json        ← W3C DTCG export
    │   └── tailwind.theme.json
    └── web/                   ← Local dev = production structure
        ├── index.html         ← Client website homepage
        ├── links.html         ← Linktree / link-in-bio (optional)
        ├── styleguide.html    ← Style guide (separate page)
        ├── css/
        │   ├── style.css      ← Design tokens (global, always loaded)
        │   ├── links.css
        │   ├── styleguide.css
        │   ├── leistungen.css ← Shared across all service pages
        │   ├── hero.css
        │   └── footer.css
        ├── js/main.js
        ├── img/               ← Logo, hero images, assets (local)
        └── components/
            ├── hero.html
            └── footer.html
```

**Why `web/`?** Mirrors the server structure exactly. Local = production.

---

## Mode 1: CI Research

Research structure defined in `docs/ci-kit.md`. Always fill every section.
Mark missing information as `⚠️ Not determinable`.

**If a CI manual (PDF) is provided:** It is the primary source for visual
identity (logo, colors with HEX/RGB/Pantone, typography, iconography).
The website is then a secondary source for copy and contact details.
Document any inconsistencies in the manual.

---

## Mode 3: Overview Page (HTML)

### New Section Patterns (current standard)

**Section 01 — Design System Bar** (always first, after hero):

```html
<section class="section reveal">
  <div class="section-label">Design System <span class="num">— 01</span></div>
  <div style="display:flex;gap:24px;align-items:center;flex-wrap:wrap">
    <div style="display:flex;align-items:center;gap:16px">
      <img src="LOGO_URL" alt="Logo" style="height:48px;width:auto;object-fit:contain">
      <div>
        <div style="font-family:'Space Grotesk',sans-serif;font-weight:600;font-size:1.1rem">CLIENT NAME</div>
        <div style="font-size:0.75rem;color:var(--text-dim)">SHORT DESCRIPTION</div>
      </div>
    </div>
    <div style="display:flex;gap:8px;align-items:center">
      <div style="width:32px;height:32px;background:COLOR1;border:2px solid COLOR1_DARK"></div>
      <div style="width:32px;height:32px;background:COLOR2;border:2px solid COLOR2_DARK"></div>
    </div>
    <a href="styleguide.html" class="btn btn-primary"
       style="margin-left:auto;font-size:0.8rem;padding:8px 16px">Style Guide →</a>
  </div>
</section>
```

**Section 02 — Compact Info Cards** (not `info-grid`):

```html
<div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(200px,1fr));gap:12px">
  <div class="card" style="padding:16px">
    <div style="font-size:0.65rem;font-weight:600;letter-spacing:0.08em;
                text-transform:uppercase;color:var(--text-micro);margin-bottom:4px">LABEL</div>
    <div style="font-size:0.85rem">VALUE</div>
  </div>
</div>
```

**Section 03 — Compact Links** (not resource lists):

```html
<div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(160px,1fr));gap:8px">
  <a href="URL" target="_blank" rel="noopener" class="card"
     style="padding:12px;text-decoration:none;color:var(--text);
            display:flex;align-items:center;gap:8px;transition:all 0.15s ease">
    <span style="font-size:0.7rem;font-weight:600;color:var(--brand)">→</span>
    <span style="font-size:0.8rem">LINK NAME</span>
  </a>
</div>
```

### Overview Page Section Order

```
01  Design System     → Logo + color swatches + style guide link (compact)
02  Overview          → 4 compact info cards (client, industry, owner, positioning)
03  Links             → Small link tiles (replaces large "External Resources" section)
04  Headline Analysis
05  Team
06  Services
07  Web Apps          → Compact cards with badges
08  Websites          → Compact cards with live/offline badge
```

**Removed from old template:**
- ❌ Brand Identity section (palette, fonts) → replaced by style guide page
- ❌ Large project overview (6 info items) → replaced by 4 compact cards
- ❌ External Resources (list format) → replaced by small link tiles

### H1 Headline Linking

Always link the H1 to the client's website:

```html
<h1>
  <a href="https://client.com" target="_blank" rel="noopener"
     style="color:inherit;text-decoration:none">
    Company Name<span class="punct">.</span>
  </a>
</h1>
```

---

## Mode 4: Style Guide (Separate Page)

The style guide is always a **separate `styleguide.html`** — never embedded
in the overview page. The overview page contains only a compact design system
bar with a link.

**9–11 Sections:**
Colors · Typography · Buttons · Badges · Inputs · Shapes · Spacing · Effects · Icons · Patterns · Do's & Don'ts

**Iframe previews** for standalone components (hero, footer):

```html
<iframe src="hero.html" class="preview-frame hero" loading="lazy"></iframe>
```

Iframe heights: hero = 700px, footer = 600px (never less — content gets cut off).

---

## CSS Architecture

### Tokens: Single Source of Truth

Tokens live **only** in `style.css`. No `:root` blocks in page-specific CSS files.

```bash
# Verify: only style.css should match
grep -l ':root' web/css/*.css
```

### Dark Mode: Centralized in `style.css`

```css
:root {
  --bg: #ffffff;
  --text: #1a1a1e;
  --brand: #3b82f6;
  /* ... light mode tokens ... */
}

/* Manual toggle via data-theme */
html[data-theme="dark"] {
  --bg: #0a0a0c;
  --text: #e8e8ec;
  /* ... dark mode tokens ... */
}

/* System preference fallback */
@media (prefers-color-scheme: dark) {
  html:not([data-theme="light"]) {
    --bg: #0a0a0c;
    --text: #e8e8ec;
    /* ... dark mode tokens ... */
  }
}
```

Page-specific CSS files must **not** contain `@media (prefers-color-scheme)` or `:root` blocks.

### CSS–JS Sync (Critical)

CSS can define states that never trigger without JavaScript. Common patterns:

| CSS class | CSS state | Required JS trigger |
|-----------|-----------|---------------------|
| `.reveal` | `opacity:0` → `.visible` | IntersectionObserver |
| `.fade-in` | `opacity:0` → `opacity:1` | IntersectionObserver |
| `#themeToggle` | `[data-theme="dark"]` | click handler + localStorage |

**Diagnosis:**

```bash
grep -n 'opacity:\s*0' web/css/*.css
grep -rn 'visible\|themeToggle' web/js/main.js
```

**Fix template for `main.js`:**

```javascript
// .reveal → IntersectionObserver
const reveals = document.querySelectorAll('.reveal');
if (reveals.length > 0) {
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('visible');
        observer.unobserve(entry.target);
      }
    });
  }, { threshold: 0.1 });
  reveals.forEach(el => observer.observe(el));
}

// Theme toggle
const themeToggle = document.getElementById('themeToggle');
if (themeToggle) {
  const saved = localStorage.getItem('site-theme');
  if (saved) document.documentElement.setAttribute('data-theme', saved);
  themeToggle.addEventListener('click', () => {
    const next = document.documentElement.getAttribute('data-theme') === 'dark'
      ? 'light' : 'dark';
    document.documentElement.setAttribute('data-theme', next);
    localStorage.setItem('site-theme', next);
  });
}
```

Rule: After every CSS audit, verify that **all** `opacity:0` elements have a JS trigger.

### `main.js` Checklist

`js/main.js` must be loaded in **all** HTML files.

```bash
grep -l 'main.js' web/*.html   # all page HTMLs must match
```

---

## CSS Extraction: Inline → External

When extracting inline `<style>` blocks from HTML:

1. **Map token names** — inline CSS may use different names (`--mint` → `--brand`)
2. **Add missing tokens to `style.css`** only
3. **Extract CSS** to `css/pagename.css`, remove `:root` block
4. **Clean HTML** — remove `<style>` tag, add `<link>` tags, move inline `style=""` attrs to classes
5. **Verify:**

```bash
grep -c '<style>' web/index.html        # must be 0
grep -c ':root' web/css/pagename.css    # must be 0
grep -c 'old-token-name' web/css/*.css  # must be 0
```

---

## Build Process

### Template Integrity Check (Before Every Build)

```python
with open(template_path, 'r') as f:
    content = f.read()
assert '</body>' in content, "TEMPLATE TRUNCATED!"
assert len(content) > 30000, f"Template too small: {len(content)} chars"
```

If the template is truncated or missing, fetch it from the server:

```python
sftp.get('/web/clientname/index.html', '/tmp/template-full.html')
```

### Build + Patch Pattern

The build script will rarely replace everything correctly on the first pass.
Expect 5–15 additional `patch()` calls after the build. This is normal.

**Typical post-build leftovers:**

- Project overview — `str.replace()` almost always fails on whitespace (8 vs 6 spaces) → patch manually
- External resource links — old template links → remove or replace
- Gallery remnants — regex removal often leaves broken HTML
- Section numbers — renumber after removing sections
- Font classes — remove/rename template-specific CSS classes
- Orphaned closing tags after section removal (see pitfall below)

**Workflow:** Build → Audit → Patch → Audit → Upload

### Whitespace Mismatch (Common Build Failure)

`str.replace()` silently fails when the search string doesn't match exactly.

```python
# WRONG — 6 spaces
t.replace('      <div class="info-item">\n        <h3>Client</h3>', ...)

# RIGHT — 8 spaces, copied verbatim from read_file output
t.replace('        <div class="info-item">\n          <h3>Client</h3>', ...)
```

Rule: Before every `str.replace()`, read the actual HTML with offset/limit
and copy the string character-for-character.

---

## Post-Build Audit Checklist

```bash
# 1. Old project name (source template) should be 0
grep -ci "old-project-name" output.html

# 2. Industry-specific categories from template
#    e.g. old service categories, team names

# 3. Template quotes and placeholder text
grep -c 'placeholder text here' output.html

# 4. Font references from old template
grep -c 'OldFont' output.html

# 5. Empty HTML elements
grep -c '<span></span>' output.html
grep -c 'rel="noopener"></a>' output.html

# 6. Domain check (if running multiple domains)
grep -c 'wrong-domain.com' output.html

# 7. Duplicate project entries
grep -c 'href="/web/projectname/"' web-index.html  # should be 1
```

**Most dangerous leftovers** (not just the project name):
- Industry-specific category names
- Template project quotes and slogans
- Font classes/imports that don't match the new CI
- Empty HTML structures that served as placeholders
- Wrong domain references

---

## Pitfalls

### Orphaned Closing Tags After Section Removal

When removing template sections, orphaned `</div></section>` tags often remain,
breaking the layout of all subsequent sections.

**Symptom:** From a certain section onwards, content spans full width — no grid,
no container.

**Detection after every patch:**

```bash
grep -n '</div>\|</section>' output.html | tail -30
```

**Rule:** After removing any section, always inspect 10–15 lines before and
after the gap. Remove extra `</div></section>` tags immediately.

**Wrong:**
```html
    </div>
  </section>
    </div>    ← ORPHANED
  </section>  ← ORPHANED
```

**Right:**
```html
    </div>
  </section>
```

### Search Marker Hit in `<head>` Instead of Body

`content.find('keyword')` may find the text in a `<meta description>` tag
before finding the actual HTML comment in the body.

**Symptom:** New card gets placed inside `<head>`, floating above the header.

**Fix — Always search for the full comment string:**

```python
# WRONG — matches meta description
idx = content.find('Section Name')

# RIGHT — matches only the real section comment
idx = content.find('<!-- ═══ Section Name ═══ -->')
```

**Verification after insert:**

```python
head_end = content.find('</head>')
card_pos = content.find('href="/web/projectname/"')
assert card_pos > head_end, "CARD IN <head>! Wrong insertion point!"
```

### Card Outside Grid Wrapper

New cards must be inserted **before** the grid's closing `</div>` tags, not after.

**Wrong structure:**
```html
      </a>      ← last card end

    </div>      ← grid end
  </div>        ← section end

  <a class="card"> ← NEW CARD HERE = outside grid = full width!
```

**Right structure:**
```html
      </a>      ← last card end
      <a class="card"> ← NEW CARD HERE = inside grid ✓
        ...
      </a>

    </div>      ← grid end
  </div>        ← section end
```

### Multi-File Font Consistency

When a font changes, update **all** files in the project.

```bash
# Find all references to old font
grep -r "OldFont" web/

# Must return 0 results after update
```

Files to update: `styleguide.html`, `hero.html`, `footer.html`, `index.html`,
`DESIGN.md`, `tokens.json`, `tailwind.theme.json`.

### `read_file` Line-Number Prefixes Corrupt Files

`read_file` returns lines with display prefixes: `"  1|<!DOCTYPE html>\n  2|<html"`.
If this output is written directly to a file, the browser renders raw text.

**Never** pass `read_file` output directly to `write_file`.
**Always** use Python's `open().read()` for file transformations:

```python
with open('/tmp/source.html', 'r') as f:
    content = f.read()
# ... transformations ...
with open('/tmp/output.html', 'w') as f:
    f.write(content)
```

### `str.replace()` with Umlauts / Special Characters in Sandboxes

Some execution sandboxes have encoding issues with special characters in string
literals. If the template build script needs to handle non-ASCII characters,
write it to a `.py` file and run via terminal:

```python
# /tmp/build_page.py
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

with open('/tmp/template.html', 'r', encoding='utf-8') as f:
    t = f.read()

t = t.replace('Old Client Name', 'New Client Name GmbH')

with open('/tmp/output.html', 'w', encoding='utf-8') as f:
    f.write(t)
```

### Passwords with Special Characters in `.env`

If the `.env` password contains shell special characters (`{`, `^`, `$`, `>`, `;`),
reading it inline in an execute sandbox will cause `SyntaxError`.

**Always** write deploy scripts to a `.py` file and run via terminal:

```python
# /tmp/upload.py
creds = {}
with open('/path/to/.env', 'r') as f:
    for line in f:
        line = line.strip()
        if '=' in line and not line.startswith('#'):
            key, val = line.split('=', 1)
            creds[key.strip()] = val.strip().strip('"').strip("'")

password = creds['SFTP_PASSWORD']
```

### CSS Search Hits in CSS Comments Instead of HTML Body

`content.find("SECTION NAME")` may find the text in a CSS comment before
finding the actual HTML body section marker.

**Fix — Use line-based search with a minimum line number:**

```python
for i, line in enumerate(lines):
    if "<!-- " in line and "SECTION NAME" in line and i > 500:  # skip CSS
        start_idx = i
        break
```

### Token Duplication

Tokens (`:root` blocks) must only be defined in `style.css`.
Page CSS files must have no `:root` blocks.

```bash
grep -l ':root' web/css/*.css   # should only return style.css
```

### Absolute Paths Break Local Dev

Server-absolute paths like `/web/projectname/` or `/css/style.css` fail
locally (file:// or localhost) because they resolve to the filesystem root.

**Always use relative paths:**

```html
<!-- WRONG (breaks locally) -->
<a href="/web/">← All Projects</a>
<link rel="stylesheet" href="/css/style.css">

<!-- RIGHT (works everywhere) -->
<a href="../">← All Projects</a>
<link rel="stylesheet" href="../css/style.css">
```

Canonical URLs (`<link rel="canonical">`) remain absolute — they're for search engines only.

### Duplicate Entries in Project Index

Before adding a new project card to the main `/web/index.html`:

```python
if f'href="/web/{projectname}/"' in existing_content:
    pass  # Already exists — don't add again
```

Also check for the highest existing project number before assigning a new one:

```python
import re
matches = re.findall(r'<span class="card-num">(\d{3})</span>', content)
numbers = sorted([int(m) for m in matches])
next_num = str(numbers[-1] + 1).zfill(3)
```

---

## SFTP Deployment

### Deploy Rules (Critical)

- Only deploy root-level files — never overwrite subdirectories without explicit user approval
- Always show a deploy list and ask for manual confirmation before uploading
- Run a pre-deploy check: internal links, external links (HTTP 200), CSS/JS consistency
- Archive old files instead of deleting: move to `_archive/`

### Always Use Python/paramiko

Never use the shell CLI for SFTP — passwords with special characters break shell escaping.

```python
import paramiko

ssh = paramiko.SSHClient()
ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
ssh.connect(host, port=22, username=user, password=password, look_for_keys=False)
sftp = ssh.open_sftp()

# Create directory if needed
try:
    sftp.mkdir('/web/projectname/')
except IOError:
    pass  # already exists

# Upload
sftp.put('/tmp/projectname-index.html', '/web/projectname/index.html')

# Update main index
sftp.get('/web/index.html', '/tmp/web-index-current.html')
# → add new card entry
sftp.put('/tmp/web-index-updated.html', '/web/index.html')

sftp.close()
ssh.close()
```

### Check Remote Structure Before Uploading

```python
# Inspect what exists on the server before deploying
for f in sftp.listdir('/web/'):
    print(f)

for f in sftp.listdir('/web/projectname/'):
    print(f)
```

Existing pages may have inline CSS instead of external files.
Local structure (`css/`, `js/`) may differ from server structure.
Rule: check server structure first, then deploy.

### Create Missing Directories

```python
for d in ['css', 'js']:
    try:
        sftp.mkdir(f'/web/projectname/{d}/')
    except IOError:
        pass  # already exists
```

---

## Local Development

**Never deploy directly to live without local testing first.**

```bash
cd workspace/clients/clientname/web/
python3 -m http.server 8765
# Browser: http://localhost:8765/
```

Optional convenience script (`web/serve.sh`):

```bash
#!/bin/bash
cd "$(dirname "$0")"
echo "🚀 http://localhost:8765/"
python3 -m http.server 8765
```

**Workflow:** Work locally → test locally → user reviews → then deploy.

---

## Link Audit (Before Every Upload)

```python
import os, re

base = '/path/to/web'
for root, dirs, files in os.walk(base):
    for f in files:
        if not f.endswith('.html'):
            continue
        html_path = os.path.join(root, f)
        with open(html_path, 'r') as fh:
            content = fh.read()

        for link in re.findall(r'href="([^"]+)"', content):
            if link.startswith(('http', 'mailto:', 'tel:', '#', 'javascript')):
                continue
            resolved = os.path.normpath(os.path.join(os.path.dirname(html_path), link))
            exists = (os.path.exists(resolved) or
                      os.path.exists(os.path.join(resolved, 'index.html')))
            if not exists:
                print(f"❌ {os.path.relpath(html_path, base)} → {link}")

        for css in re.findall(r'href="([^"]+\.css)"', content):
            if css.startswith('http'):
                continue
            resolved = os.path.normpath(os.path.join(os.path.dirname(html_path), css))
            if not os.path.exists(resolved):
                print(f"❌ CSS: {os.path.relpath(html_path, base)} → {css}")

        for js in re.findall(r'src="([^"]+\.js)"', content):
            if js.startswith('http'):
                continue
            resolved = os.path.normpath(os.path.join(os.path.dirname(html_path), js))
            if not os.path.exists(resolved):
                print(f"❌ JS: {os.path.relpath(html_path, base)} → {js}")
```

Goal: **0 broken internal links** before any deploy.

---

## HTML Rendering to PNG (Playwright)

For pixel-accurate screenshots of HTML files:

```bash
cd /tmp && npm init -y && npm install playwright
npx playwright install chromium
```

```javascript
// /tmp/render.js
const { chromium } = require('playwright');
(async () => {
  const browser = await chromium.launch({ headless: true });
  const page = await browser.newPage();
  await page.setViewportSize({ width: 1080, height: 1920 });

  // Always use a local HTTP server, not file://
  await page.goto('http://localhost:8765/', { waitUntil: 'networkidle' });

  // Always use buffer mode — path parameter can produce corrupt PNGs
  const buf = await page.screenshot({ type: 'png' });
  require('fs').writeFileSync('/tmp/output.png', buf);

  await browser.close();
})();
```

**Never** use `page.screenshot({ path: '...' })` — it can produce corrupt files.
**Always** use buffer mode + `writeFileSync`.

macOS fallback (reliable but no full-page):

```bash
qlmanage -t -s 1280 -o /tmp/ file.html
```

### Viewport Sizes

| Format | Width × Height | Use |
|--------|---------------|-----|
| Instagram Story | 1080 × 1920 | 9:16 vertical |
| Instagram Post | 1080 × 1080 | 1:1 square |
| LinkedIn Banner | 1584 × 396 | 4:1 wide |
| Desktop Preview | 1280 × 800 | General preview |

---

## Design Audit Checklist

Before every deploy, check CSS against quality principles:

1. `transition: all` → replace with specific properties
2. `:hover` without `@media (hover: hover)` guard → add guard
3. Buttons without `:active { transform: scale(0.97) }` → add
4. Missing `prefers-reduced-motion` → add at end of every CSS file

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## WCAG Contrast Reference

| Color | On White | On Dark (#1a1a1e) | Rule |
|-------|----------|-------------------|------|
| `#3b82f6` Blue | 3.68:1 ✗ | 5.53:1 ✓ | Use dark text on blue |
| `#1d4ed8` Deep Blue | 6.70:1 ✓ | — | White text allowed |
| `#10b981` Green | 2.54:1 ✗ | 6.84:1 ✓ | **Always** dark text on green |
| `#ffff00` Yellow | — | 16.16:1 ✓ | Dark text on yellow |

Light colors (green, yellow) always need dark text.

---

## Project Index Card Format

When adding a new card to the main project index:

```html
<a class="card" data-cat="misc" href="/web/projectname/">
  <span class="card-num">018</span>
  <span class="card-title">Company Name — Category</span>
  <span class="card-desc">One-sentence description.</span>
  <span class="card-arrow">→ View</span>
</a>
```

`data-cat` values: `zahn`, `gastro`, `it`, `coach`, `misc`, `app`, `med` (adapt to your taxonomy).

**Never** use `<div class="card-body"><h3>` — always use `<span>` elements as shown above.

---

## Systematic Corrections: Propagate Everywhere

When a correction is needed (wrong domain, wrong sender, wrong name),
don't fix only the current file. Check all affected locations:

- `docs/ci-kit.md` — source of truth for contact details
- All HTML overview pages
- Templates
- Email signatures
- Skill/workflow documentation
- Memory / project notes

---

## Quick Reference: CSS Tokens

Example token structure for `:root` in `style.css`:

```css
:root {
  --brand:      #3b82f6;   /* primary accent */
  --brand-rgb:  59,130,246; /* for rgba() */
  --brand-deep: #1d4ed8;   /* dark variant for text on light BG */
  --brand-2:    #f59e0b;   /* secondary accent (optional) */
}
```

---

## Logo Rules

- **Always** use the client's original logo — never substitute icon libraries
- Store SVG locally in `web/img/`
- For dark backgrounds: provide a white variant (`fill:#ffffff`)
- Never reference logos from external WordPress or CDN URLs in production

---

## References

- `docs/DESIGN.md` — Design system spec (YAML + Markdown)
- `docs/ci-kit.md` — Brand research
- `references/deploy-workflow.md` — SFTP deploy script template
- `references/leistungsseiten-patterns.md` — Service page patterns and pitfalls
- `references/design-audit-checklist.md` — CSS quality checklist
- `references/css-js-sync.md` — CSS/JS sync patterns
- `references/dark-light-toggle.md` — Theme toggle implementation
