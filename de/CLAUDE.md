# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A personal academic homepage for Johannes L. Hörmann (computational nanotribologist), built on the [al-folio](https://github.com/alshedivat/al-folio) Jekyll theme (v0.16.3) and deployed to `jotelha.github.io` via GitHub Pages.

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
| Homepage bio | `_pages/about.md` (EN), `_pages/zh/about.md` (ZH), `_pages/ja/about.md` (JA) |
| Publications | `_bibliography/papers.bib` (BibTeX) |
| CV (structured) | `assets/json/resume.json` (primary; jsonresume.org schema) |
| CV (YAML fallback) | `_data/cv.yml` (only used if resume.json absent — currently contains Einstein placeholder) |
| CV PDFs | `assets/pdf/` — trilingual: `2025-07-30-CV-academic-{en,de,cn}.pdf` |
| Social links | `_data/socials.yml` |
| News items | `_news/` (announcements disabled in `_pages/about.md`) |
| Projects | `_projects/` (currently all template placeholders) |
| Co-author highlighting | `_data/coauthors.yml` |
| GitHub repos shown | `_data/repositories.yml` |
| UI translation strings | `_data/lang/{en,zh,ja}.yml` |

## Key config

`_config.yml` is the central control file. Important sections:
- `scholar:` — name fields for author highlighting in publications (`last_name: [Hörmann]`)
- `exclude:` — several template pages are excluded from the build (placeholder projects, blog, profiles, teaching, dropdown pages, all `announcement_*.md`)
- Jekyll Scholar (`jekyll/scholar`) auto-generates the publications page from `papers.bib`, grouped by year descending
- CV page (`_pages/cv.md`) points to `cv_pdf: /assets/pdf/2025-07-30-CV-academic-en.pdf` (v0.16.3+ requires the full path prefix)
- `_data/socials.yml` now also has a `cv_pdf` field (new in v0.16.3) — keep it in sync with `_pages/cv.md`

## Liquid templates

Layouts are in `_layouts/`, partials in `_includes/`. They use Liquid syntax (`.liquid` extension). The CV page renders from `assets/json/resume.json` via `_layouts/cv.liquid` and `_includes/resume/`.

## Multilingual support

The site uses [jekyll-polyglot](https://polyglot.untra.io/) for EN/ZH/JA support (branch `feature/multilingual`). Languages are configured in `_config.yml` under `languages:`.

**URL structure:** English at `/`, Chinese at `/zh/`, Japanese at `/ja/`.

**How translated pages work:** Each language has its own page file with a `lang:` frontmatter key. Polyglot filters `site.pages` per build so only the correct language's pages appear in the nav. The original `_pages/*.md` files carry `lang: en` so they are excluded from non-English builds.

| Language | Page files |
|---|---|
| English (default) | `_pages/*.md` (with `lang: en`) |
| Chinese | `_pages/zh/*.md` (with `lang: zh`) |
| Japanese | `_pages/ja/*.md` (with `lang: ja`) |

**Adding a new translated page:** Create `_pages/{lang}/page-name.md` with matching `permalink:`, `nav_order:`, and `lang: {lang}` frontmatter. No other files need changing.

**Adding a new language:** Add the language code to `languages:` in `_config.yml`, create `_data/lang/{code}.yml` for UI strings, and add a `{% when '{code}' %}` case to `_includes/language_switcher.liquid`.

**UI translation strings** (`_data/lang/{en,zh,ja}.yml`) are available in templates as `site.data.lang[site.active_lang].ui.key` — not yet wired into layouts, reserved for future footer/button translation.

## Rebasing `feature/multilingual` onto upstream al-folio

The multilingual branch modifies exactly four theme files. After `git rebase main`, check each for conflicts and re-apply the listed one-line change if needed:

| File | Change to re-apply |
|---|---|
| `_config.yml` | polyglot block after `lang: en` + `- jekyll-polyglot` in plugins list |
| `_layouts/default.liquid` | `site.active_lang \| default: site.lang` in `<html lang="...">` |
| `_includes/header.liquid` | `{% include language_switcher.liquid %}` before `</ul>` |
| `Gemfile` | `gem 'jekyll-polyglot'` as first entry in `:jekyll_plugins` group |

All files under `_pages/zh/`, `_pages/ja/`, `_data/lang/`, and `_includes/language_switcher.liquid` are pure additions and will never conflict during a rebase.

## Adding/updating content

- **New publication**: add a BibTeX entry to `_bibliography/papers.bib`. Add `selected={true}` to feature it on the homepage.
- **New project**: create `_projects/N_project.md` and remove the corresponding glob from the `exclude:` list in `_config.yml`.
- **Enable news/blog**: set `enabled: true` under `announcements:` or `latest_posts:` in `_pages/about.md`.
- **Update CV**: edit `assets/json/resume.json` (authoritative source) and replace the PDF in `assets/pdf/`.
