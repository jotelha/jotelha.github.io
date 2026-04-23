# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A personal academic homepage for Johannes L. Hörmann (computational nanotribologist), built on the [al-folio](https://github.com/alshedivat/al-folio) Jekyll theme and deployed to `jotelha.github.io` via GitHub Pages.

## Local development

The recommended way to run locally is Docker:

```bash
docker compose pull && docker compose up
# site serves at http://localhost:8080 with live reload
```

Or with the slim image (~100MB):

```bash
docker compose -f docker-compose-slim.yml up
```

Build only (no server):

```bash
bundle exec jekyll build
```

Format Liquid/HTML/CSS/JS with Prettier:

```bash
npx prettier --write .
```

Pre-commit hooks check trailing whitespace, EOF newlines, YAML validity, and large files.

## Deployment

Pushing to `main` triggers the GitHub Actions deploy workflow, which builds and pushes to the `gh-pages` branch. No manual deploy step needed.

## Content architecture

All personal content lives in these locations — everything else is theme infrastructure:

| What | Where |
|---|---|
| Homepage bio | `_pages/about.md` |
| Publications | `_bibliography/papers.bib` (BibTeX) |
| CV (structured) | `assets/json/resume.json` (primary; jsonresume.org schema) |
| CV (YAML fallback) | `_data/cv.yml` (only used if resume.json absent — currently contains Einstein placeholder) |
| CV PDFs | `assets/pdf/` — trilingual: `2025-07-30-CV-academic-{en,de,cn}.pdf` |
| Social links | `_data/socials.yml` |
| News items | `_news/` (announcements disabled in `_pages/about.md`) |
| Projects | `_projects/` (currently all template placeholders) |
| Co-author highlighting | `_data/coauthors.yml` |
| GitHub repos shown | `_data/repositories.yml` |

## Key config

`_config.yml` is the central control file. Important sections:
- `scholar:` — name fields for author highlighting in publications (`last_name: [Hörmann]`)
- `exclude:` — several template pages are excluded from the build (placeholder projects, blog, profiles, teaching, dropdown pages, all `announcement_*.md`)
- Jekyll Scholar (`jekyll/scholar`) auto-generates the publications page from `papers.bib`, grouped by year descending
- CV page (`_pages/cv.md`) points to `cv_pdf: 2025-07-30-CV-academic-en.pdf`

## Liquid templates

Layouts are in `_layouts/`, partials in `_includes/`. They use Liquid syntax (`.liquid` extension). The CV page renders from `assets/json/resume.json` via `_layouts/cv.liquid` and `_includes/resume/`.

## Adding/updating content

- **New publication**: add a BibTeX entry to `_bibliography/papers.bib`. Add `selected={true}` to feature it on the homepage.
- **New project**: create `_projects/N_project.md` and remove the corresponding glob from the `exclude:` list in `_config.yml`.
- **Enable news/blog**: set `enabled: true` under `announcements:` or `latest_posts:` in `_pages/about.md`.
- **Update CV**: edit `assets/json/resume.json` (authoritative source) and replace the PDF in `assets/pdf/`.
