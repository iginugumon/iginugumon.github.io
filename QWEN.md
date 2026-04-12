# Project Context: iginugumon.github.io

## Overview

A personal anonymous blog built with **Jekyll**, hosted on GitHub Pages. The site uses the `jekyll-theme-slate` theme and features a simple, reverse-chronological post layout on the homepage.

## Tech Stack

- **Jekyll** — Static site generator
- **GitHub Pages** — Hosting platform
- **Theme:** `jekyll-theme-slate`

## Project Structure

```
iginugumon/
├── _config.yml          # Jekyll configuration
├── index.htm            # Custom homepage layout (lists all posts)
├── _posts/              # Blog posts in Jekyll's required naming format
│   └── 2026-04-12-hello-world.md
├── _site/               # Generated output (gitignored)
├── .gitignore
└── README.md
```

## Key Files

| File | Description |
|---|---|
| `_config.yml` | Jekyll config: sets title, description, theme, and pagination |
| `index.htm` | Custom homepage template — iterates over `site.posts` and renders each with date and content |
| `_posts/` | Directory for blog posts. Files must follow the `YYYY-MM-DD-slug.md` naming convention |

## Building & Running

### Local Development

```bash
# Install dependencies (if a Gemfile exists)
bundle install

# Serve locally with live reload
bundle exec jekyll serve
```

The site will be available at `http://localhost:4000`.

### Deployment

The site is deployed automatically via **GitHub Pages** when changes are pushed to the main branch.

## Adding a New Post

Create a new Markdown file in `_posts/` following the naming convention `YYYY-MM-DD-title.md` with front matter:

```markdown
---
layout: post
title: "Your Post Title"
date: YYYY-MM-DD
---
Your content here.
```

## Conventions

- Posts use the `post` layout and are written in Markdown
- The homepage (`index.htm`) uses a custom layout without a sidebar — just a clean list of posts with dates
- No custom CSS is defined; styling comes entirely from the `jekyll-theme-slate` theme
