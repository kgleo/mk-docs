Here is a complete, professional README.md file that documents everything you've built – from the MkDocs setup, to the custom theme, to the advanced in‑browser editor.

To add this, go to your repository root and click "Add file" → "Create new file". Name it exactly README.md, paste the content below, and commit it.

---
# 📚 MkDocs Space – Full‑Width Documentation Site

Welcome to my documentation repository!  
This project is a fully browser‑based, no‑desktop‑required documentation site built with **MkDocs**, the **Material Theme**, and **GitHub Pages**.

It features a clean, Confluence‑inspired layout, an advanced in‑browser editor, and a fully automated build process – all managed directly from the GitHub web interface.

---

## 🚀 Live Site

> **https://kaunghtetminkght-prog.github.io/mk-docs/**

---

## ✨ Key Features

- **Edge‑to‑Edge Layout** – No distracting cards or borders; content stretches 100% across the screen, making it mobile‑friendly.
- **Confluence‑style Interface** – Clean white header, blue accents, and a page tree sidebar (with integrated table of contents).
- **In‑Browser File Editor** – Create, edit, and delete `.md` pages directly from the browser.
- **Batch Upload** – Upload entire folders or ZIP archives that auto‑extract into your `docs/` folder.
- **Automated Build** – GitHub Actions automatically rebuilds your static site every time you push a change.

---

## 📁 Repository Structure

```

.
├── .github/
│   └── workflows/
│       └── static.yml          # GitHub Actions workflow for deployment
├── docs/
│   ├── assets/                 # (Optional) Images, logos, etc.
│   ├── stylesheets/
│   │   └── extra.css           # Custom CSS (edge‑to‑edge, Confluence theme)
│   ├── editor.html             # In‑browser file manager & Markdown editor
│   └── index.md                # Homepage content
├── mkdocs.yml                  # MkDocs configuration (theme, features, plugins)
└── README.md                   # This file

```

---

## ⚙️ How It Works (Configuration)

### 1. `mkdocs.yml` – The Core Setup

This file controls the entire look and behavior of the site.

```yaml
site_name: My Space
site_url: https://kaunghtetminkght-prog.github.io/mk-docs/

theme:
  name: material
  palette:
    - scheme: default
      primary: white
      accent: blue
      toggle:
        icon: material/weather-night
        name: Switch to dark mode
    - scheme: slate
      primary: blue
      accent: light-blue
      toggle:
        icon: material/weather-sunny
        name: Switch to light mode

  font:
    text: Inter
    code: JetBrains Mono

  features:
    - navigation.sections      # Sidebar sections like a page tree
    - navigation.expand        # Expand tree by default
    - navigation.top           # Back to top button
    - navigation.indexes       # Clickable index.md pages
    - toc.integrate            # Moves the TOC into the left sidebar
    - search.suggest
    - search.highlight

extra_css:
  - stylesheets/extra.css

extra:
  generator: false             # Removes "Made with Material"
  social:
    - icon: fontawesome/brands/github
      link: https://github.com/kaunghtetminkght-prog

copyright: '© 2026 Your Name'
```

---

2. docs/stylesheets/extra.css – Full‑Width Theme

This CSS removes the default card layout and makes the content stretch edge‑to‑edge while keeping a professional look.

Highlights:

· Removes max‑width restrictions.
· Eliminates white card backgrounds and borders.
· Adds spacious padding for large screens and comfortable margins for mobile.
· Mimics Atlassian (Confluence) colors and styling.

---

3. .github/workflows/static.yml – Automated Deployment

This workflow automatically deploys your site whenever you push changes to the main branch. It uploads your static files (including the generated site/ folder) to GitHub Pages.

---

🛠️ The In‑Browser Editor (docs/editor.html)

This is the most powerful tool in this repository. It allows you to manage your documentation entirely from your browser—no terminal or desktop apps required.

What It Can Do

Feature Description
Single File Editor Create or edit .md files directly.
Folder Upload Upload entire folders from your device; the structure is preserved.
ZIP Upload Upload a .zip file; it auto‑extracts and pushes all contents to docs/.
Strip Root Folder Removes the top‑level folder name from extracted paths (toggle on/off).
Progress Log See real‑time success/failure feedback for every file.

How to Use the Editor

1. Open: https://kaunghtetminkght-prog.github.io/mk-docs/editor.html
2. Enter your GitHub Personal Access Token (requires repo scope).
   · Generate one here: GitHub Token Settings
   · Store it securely in your browser using the "Save Token" button.
3. Use the "Create / Edit Single Page" section to add or modify a single file.
4. Use the "Batch Upload" section to upload a folder or a ZIP file.
5. Wait ~1 minute for GitHub Actions to rebuild the site after each upload.

---

📝 Adding / Editing Content (Quick Guide)

Option A: Using the Editor (Recommended)

· Go to the Editor URL.
· Add a token, write your Markdown, and click "Save Single File".
· Or upload a whole folder/ZIP to add multiple pages at once.

Option B: Directly on GitHub

· Navigate to docs/ folder in your repository.
· Click "Add file" → "Create new file".
· Name it docs/new-page.md, write your content, and commit.
· Wait ~1 minute for the rebuild.

---

🎨 Customizing the Theme

Want to tweak the colors or padding?

1. Edit docs/stylesheets/extra.css directly on GitHub.
2. Adjust the :root variables at the top for colors, or modify the .md-typeset padding sections for spacing.
3. Commit and wait for the rebuild.

---

📱 Mobile Responsiveness

The edge‑to‑edge design makes it perfect for mobile viewing. The sidebar collapses into a hamburger menu, and the text remains highly readable without unnecessary whitespace.

---

🧩 Built With

· MkDocs – Static site generator for project documentation.
· Material for MkDocs – The theme powering the look.
· GitHub Pages – Hosting.
· JSZip – Used in the editor for ZIP extraction.
· GitHub API – Powers the in‑browser file uploads.

---

📄 License

This project is open for personal and commercial use. Modify it freely to suit your documentation needs.

---

🙌 Acknowledgments

Built entirely in the browser using the GitHub web interface – no desktop tools were used in the creation or maintenance of this site.

---

```

---

### ✅ Why this README works

| Section | Purpose |
| :--- | :--- |
| **Live Site** | Immediate access to the final product. |
| **Key Features** | Shows off the edge‑to‑edge design and the editor. |
| **File Structure** | Explains where everything lives (good for your own reference). |
| **Configuration** | Documents your exact `mkdocs.yml` setup. |
| **Editor Guide** | Teaches anyone (including future you) how to use the complex editor. |
| **Quick Content Guide** | Two ways to add pages – via editor or directly. |
| **Customizing** | Shows how to tweak the CSS. |
| **Responsive/Mobile** | Highlights why you removed the cards. |

Commit this to your repo, and visitors will immediately understand what they're looking at and how to use it. 🚀
