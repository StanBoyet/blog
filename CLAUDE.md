# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Personal blog "This is Stan" (https://stan.boyet.me) built with Jekyll using the Mediumish theme.

## Commands

```bash
bundle install              # Install Ruby dependencies
bundle exec jekyll serve    # Dev server at http://localhost:4000
bundle exec jekyll build    # Build static site to ./_site/
docker-compose up           # Alternative: run via Docker
```

Deployment is automatic via GitHub Actions on push to `master` → GitHub Pages.

## Architecture

Jekyll static site generator with Liquid templating:

- `_posts/` — Published blog posts as Markdown (`YYYY-MM-DD-title.md`)
- `_drafts/` — Unpublished drafts (not deployed)
- `_pages/` — Static pages (About, Contact, etc.)
- `_layouts/` — Page templates (`default.html`, `post.html`, `page.html`, etc.)
- `_includes/` — Reusable HTML partials (post cards, pagination, TOC, Mermaid diagrams)
- `_sass/` — SCSS stylesheets
- `assets/` — Images, fonts, static files
- `_config.yml` — Jekyll configuration (site metadata, plugins, permalink structure)

## Post Front Matter

Posts use YAML front matter. Key fields:

```yaml
---
layout: post
title: "Post Title"
author: stan
tags: [tag1, tag2]
image: assets/images/filename.jpg  # OG image source
featured: true  # optional, shows in featured section
---
```

Permalink structure is `/:title/` (configured in `_config.yml`).

## Git

Never include "Co-Authored-By" in commit messages, PR descriptions, or anywhere else.

## Plugins

- `jekyll-paginate` — 6 posts per page
- `jekyll-og-image` — Auto-generates Open Graph images from post metadata
- `jekyll-sitemap`, `jekyll-feed`, `jekyll-seo-tag`
