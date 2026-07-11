# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A Hugo static site — Rodrigo Dias's personal portfolio/CV site — built on the third-party [hugo-noir](https://github.com/prxshetty/hugo-noir) theme, consumed as a git submodule at `themes/hugo-noir`. This repo holds only site configuration and content; almost all layout/CSS/i18n logic lives in the submodule and should not normally be edited from here.

## Commands

Requires Hugo **Extended**, v0.92.0+.

```bash
hugo server -D          # local dev server with drafts, live reload
hugo                    # build production site into public/
hugo new posts/my-post.md   # scaffold a new post using archetypes/default.md
```

Submodule setup (needed after a fresh clone, since `themes/hugo-noir` is a git submodule):

```bash
git submodule update --init --recursive
```

There is no linter, test suite, or package.json at the repo root — this project is config/content only.

## Architecture

- **`hugo.toml`** — the site's single config file. `[params]` holds top-level site identity (name, location, description, social links) and drives the homepage/theme. `[[menu.main]]` entries define primary nav. `[[params.experience]]` entries define the work-history list rendered by the theme's `experience.html` layout — each entry has `company`, `role`, `period`, `description`, `responsibilities`.
- **`content/`** — Hugo content pages (currently just `content/posts/`). This site is *not* set up for multilingual `contentDir` per the theme's convention (e.g. `content/en/`) — content lives directly under `content/`, so language-specific data files are unused in practice.
- **`data/en/tech.toml`** — populates the homepage's two tech-stack carousel rows (`row1`, `row2`) as `{ icon, name }` pairs. `icon` values are [Devicon](https://devicon.dev/) CSS classes (e.g. `devicon-go-plain`).
- **`archetypes/default.md`** — front-matter template used by `hugo new`.
- **`themes/hugo-noir/`** — the theme submodule. Key layout files: `layouts/_default/baseof.html` (base template), `layouts/index.html` (homepage), `layouts/_default/{about,experience,projects,contact,blogs,single,list}.html` (page-type templates), `layouts/partials/{header,footer}.html`. Do not edit these directly for site-specific changes — see override pattern below.
- **`themes/hugo-noir/i18n/*.toml`** — theme translation strings (en/es/fr/zh-tw), only relevant if multilingual mode is enabled in `hugo.toml`.
- **`public/`** — Hugo's build output; generated, not source.

## Customizing without touching the submodule

Per the theme's own conventions:
- To override a template, copy the file from `themes/hugo-noir/layouts/...` into `layouts/...` at the repo root with the same relative path, then edit the copy.
- To override/extend a partial, do the same under `layouts/partials/`.
- For minor CSS tweaks, create `assets/css/custom.css` at the repo root — the theme loads it automatically.
- Static assets (images, fonts) referenced from content or config (e.g. `profile_image`) go under `static/`.

## Data-driven sections

Some theme sections read from Hugo data files rather than `hugo.toml`, organized by language under `data/<lang>/`:
- `data/<lang>/author.toml` — author identity/social links (alternative to `[params]` in `hugo.toml`).
- `data/<lang>/tech.toml` — tech carousel (in use here, English only).
- `data/<lang>/experience.toml` — work experience (this site currently uses `[[params.experience]]` in `hugo.toml` instead).

When adding experience entries, note the theme supports an optional `company_link` field that turns the company name into a hyperlink.
