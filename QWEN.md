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
├── index.html           # Homepage with paginated posts
├── _layouts/
│   └── default.html     # Base HTML layout
├── _posts/              # Blog posts in Jekyll's required naming format
│   └── *.md
├── _staging/            # Staging directory (if used)
├── assets/
│   ├── css/             # Custom stylesheets
│   └── images/          # Image assets
├── _site/               # Generated output (gitignored)
├── .gitignore
└── README.md
```

## Key Files

| File | Description |
|---|---|
| `_config.yml` | Jekyll config: sets title, description, theme, and pagination (`paginate: 10`, `paginate_path: "/page/:num/"`, `jekyll-paginate` plugin) |
| `index.html` | Custom homepage template — iterates over `paginator.posts` (10 per page) with pagination navigation |
| `_layouts/default.html` | Base layout with header, content area, and footer |
| `_posts/` | Directory for blog posts. Files must follow the `YYYY-MM-DD-slug.md` naming convention |
| `assets/css/` | Custom CSS stylesheets |
| `assets/images/` | Image assets used in posts |

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
- The homepage (`index.html`) displays 10 posts per page with pagination navigation (Previous/Next and page numbers)
- Styling uses `jekyll-theme-slate` theme with possible custom CSS in `assets/css/`
- Images are stored in `assets/images/`
