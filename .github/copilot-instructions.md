# Copilot Instructions for mk-docs

## Repository Overview

**mk-docs** is a browser-based documentation site builder powered by **MkDocs** and the **Material theme**. It enables users to create, edit, and manage documentation entirely through their browser using GitHub Pages for hosting. The site features:
- Edge-to-edge, card-free layout (Confluence-inspired design)
- In-browser rich-text editor (WYSIWYG) for editing Markdown
- Batch upload capabilities for folders and ZIP files
- Automated GitHub Actions deployment pipeline

**Tech Stack:** MkDocs, Material Theme, Python, JavaScript (Toast UI Editor, JSZip, Sortable.js), YAML, HTML/CSS

**Repository Size:** ~131 KB

---

## Build & Deployment Instructions

### Prerequisites
- Python 3.x (any recent version)
- No Node.js or npm required for the build itself
- GitHub Actions handles all deployment automatically

### Local Build (Optional - for testing)

```bash
# 1. Install dependencies
pip install mkdocs-material mkdocs-git-revision-date-localized-plugin

# 2. Build the site locally
mkdocs build

# 3. Preview locally (optional)
mkdocs serve
```

**Expected Output:** A `site/` directory containing the complete static HTML site.

### Deployment via GitHub Actions

✅ **ALWAYS WORKS** — When you push changes to the `main` branch:

1. The workflow `.github/workflows/deploy.yml` triggers automatically
2. **Workflow steps:**
   - Checkout code (actions/checkout@v4)
   - Setup Python 3.x (actions/setup-python@v5)
   - Install: `mkdocs-material` and `mkdocs-git-revision-date-localized-plugin`
   - Build: `mkdocs build` → generates `site/` directory
   - Upload artifact and deploy to GitHub Pages (actions/upload-pages-artifact@v3, actions/deploy-pages@v4)
3. Site is live at: `https://kaunghtetminkght-prog.github.io/mk-docs/` (~1 minute after push)

**Key Commands (validated):**
```bash
pip install mkdocs-material mkdocs-git-revision-date-localized-plugin
mkdocs build
```

**No manual deployment needed.** All validation happens in Actions. If a push doesn't appear on the site within 2 minutes, check the Actions tab for build errors.

---

## Repository Layout & Architecture

### Root Files
- **README.md** — Full documentation of the project, including editor setup guide
- **mkdocs.yml** — MkDocs configuration; controls theme, navigation, plugins, and site metadata
- **.github/workflows/deploy.yml** — GitHub Actions workflow for automatic builds and GitHub Pages deployment

### Documentation Source
- **docs/** — All Markdown content and editor HTML
  - **index.md** — Homepage (`# Welcome to My Docs`)
  - **docs/*.md** — Content pages (BPMN Prompt.md, EWA files, etc.)
  - **editor.html** — In-browser WYSIWYG editor (~42 KB, ~950 lines of JavaScript)
  - **stylesheets/extra.css** — Custom CSS for edge-to-edge Confluence-inspired theme (~160 lines)

### Key Files
- **mkdocs.yml** (lines 1–49) — Configuration:
  - Theme: Material (`name: material`)
  - Features: navigation sections, expand, footer navigation, integrated TOC
  - Fonts: Inter (text), JetBrains Mono (code)
  - Nav order defined at lines 44–48; edit this to control page order
  - Site URL: `https://kaunghtetminkght-prog.github.io/mk-docs/`

- **docs/stylesheets/extra.css** (lines 1–160) — Theme customization:
  - Lines 6–12: CSS variables (colors: white #ffffff, text #172b4d, blue #0052cc, border #e9ecf3)
  - Lines 64–67: Remove max-width constraint for edge-to-edge layout
  - Lines 84–98: Typography padding (responsive; 20px mobile, 80px desktop)
  - Lines 100–141: Typography styling (headings, links, code blocks)
  - **Implication:** Any width changes or layout fixes go here

- **docs/editor.html** (lines 1–948) — Browser-based editor:
  - Token storage: localStorage (line 291)
  - GitHub API calls: Fetch wrapper with optional CORS proxy (lines 278–286, 313–324)
  - Rich text editor: Toast UI (lines 245–249, 441–455)
  - Navigation YAML parser: js-yaml (lines 468–493)
  - Batch upload: JSZip library (lines 221–226)
  - **Key functions:** loadNavigation(), editPage(), saveEditedPage(), saveNavigation()
  - **API Endpoints used:** /repos/{owner}/{repo}/contents/ (GET, PUT, DELETE)

### CI/CD Pipeline (`.github/workflows/deploy.yml`)

**Triggers:** On push to `main` branch only

**Build Job:**
1. Checks out repo (v4)
2. Sets up Python 3.x
3. Installs MkDocs packages: `pip install mkdocs-material mkdocs-git-revision-date-localized-plugin`
4. Builds site: `mkdocs build` → outputs to `site/` directory
5. Uploads `site/` as artifact

**Deploy Job:**
1. Requires artifact from build job
2. Sets GitHub Pages environment
3. Deploys to Pages using actions/deploy-pages@v4
4. **Result:** Site goes live at repository Pages URL within ~60 seconds

**Validation Steps:**
- Push to main branch
- Check `.github/workflows/deploy.yml` execution in Actions tab
- Verify no Python/MkDocs errors in the build log
- Confirm site updates at live URL within 60–90 seconds

---

## Making Changes: What You Need to Know

### Adding/Editing Documentation Pages
- **Edit Markdown files:** Edit any `.md` file in `docs/` directly. Commit to `main` → Actions rebuilds automatically.
- **Update navigation:** Edit `mkdocs.yml` lines 44–48 (`nav:` section) to control page order and grouping.
- **Syntax:** YAML list format. Example:
  ```yaml
  nav:
    - Home: index.md
    - BPMN: BPMN Prompt.md
    - EWA Overview: EWA 4.0.md
  ```
- **Always commit changes to `main`** — the only branch that triggers deployment.

### Customizing Theme/Styling
- Edit `docs/stylesheets/extra.css`:
  - Colors: Modify `:root` variables (lines 6–12)
  - Layout: Adjust `.md-typeset` padding (lines 84–98) for spacing
  - Typography: Update heading/link styles (lines 100–141)
- Test locally: Run `mkdocs serve` to preview changes.

### Updating Configuration
- Edit `mkdocs.yml`:
  - Site name/URL: Lines 1–2
  - Theme settings: Lines 4–40
  - Features: Lines 22–29 (enable/disable navigation features here)
  - Copyright: Line 41
- **Do not remove critical sections** (theme, features, extra_css) — they are required.

### Critical Validations Before Committing
1. **YAML Syntax:** mkdocs.yml must be valid YAML (proper indentation, no tabs).
2. **File References:** All files referenced in `nav:` must exist in `docs/` directory.
3. **Markdown Syntax:** No syntax errors in `.md` files (MkDocs will fail if invalid).
4. **CSS Validity:** No CSS syntax errors in `extra.css`.

**How to validate:**
- Clone repo locally and run `mkdocs build` to catch errors before pushing.
- Or: Push and check Actions tab for errors; they appear instantly.

---

## Common Pitfalls & Solutions

| Issue | Cause | Solution |
|-------|-------|----------|
| Build fails with "module not found" | Python packages not installed | Ensure workflow uses: `pip install mkdocs-material mkdocs-git-revision-date-localized-plugin` |
| Site doesn't update after push | Changes not on `main` branch | Ensure push is to `main` (not a feature branch). Check Actions tab. |
| Navigation shows broken links | File referenced in `nav:` doesn't exist in `docs/` | Add missing `.md` file or update `nav:` to correct path |
| Editor.html fails to load | CDN timeout or network block | Toggle "Use CORS Proxy" option in editor or check browser console |
| CSS changes don't appear | Cache issue or file not referenced | Clear browser cache; confirm `extra_css: - stylesheets/extra.css` in mkdocs.yml |
| YAML parse error in mkdocs.yml | Indentation or syntax error | Use spaces (not tabs); validate with a YAML linter |

---

## Project Architecture Summary

```
mk-docs/
├── README.md                          # Full user guide + feature docs
├── mkdocs.yml                         # Theme, nav, plugins, metadata
├── .github/
│   └── workflows/deploy.yml           # GitHub Actions: build → GitHub Pages
└── docs/
    ├── index.md                       # Homepage
    ├── *.md                           # Content pages (BPMN, EWA, etc.)
    ├── editor.html                    # In-browser WYSIWYG editor
    └── stylesheets/
        └── extra.css                  # Custom CSS (edge-to-edge theme)
```

**Design Philosophy:** Minimal, static-first, browser-native. No build step required locally; automation happens in Actions. Content is pure Markdown; theme is pure CSS overrides.

---

## Trust These Instructions

These instructions are authoritative for this repository. They include:
- Validated build commands
- Exact workflow behavior
- File paths and their purposes
- Common errors and workarounds

**Only search for additional information if:**
- A build command fails with an error not listed here
- MkDocs or Material theme version compatibility issues arise
- GitHub Actions workflow syntax changes (unlikely)

Otherwise, follow these instructions directly to avoid redundant exploration.
