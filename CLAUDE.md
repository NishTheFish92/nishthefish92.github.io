# CLAUDE.md

Guidance for future development sessions on this repo.

## What this is

A Jekyll site for the GitHub Pages **user site** `nishthefish92.github.io`
(served at `https://nishthefish92.github.io`). It's a personal
blog/portfolio with a retro CLI/terminal aesthetic.

For end-user instructions (publishing, writing posts, editing content), see
`GUIDE.md` — that's the primary doc for the site owner. This file is for
whoever is writing code/templates for the site.

## Current approach: terminal-styled, normal navigation

The site *looks* like a terminal window (`_includes/nav.html` renders a
title-bar-style box with red/yellow/green dots and a prompt-style nav), but
navigation is plain `<a>` links to real pages — `/`, `/about/`, `/blog/`,
`/projects/`. No JavaScript is required for the site to be fully usable or
indexable.

## Conventions to preserve

- **Color palette as CSS variables**: all theme colors live in `:root` at the
  top of `assets/css/style.css` (`--bg`, `--fg`, `--accent`, etc.). Any new
  styles should reference these variables, not hardcoded hex values, so the
  whole site can be recolored from one place.
- **GitHub-Pages-supported plugins only**: `_config.yml` lists
  `jekyll-feed`, `jekyll-seo-tag`, `jekyll-sitemap`. Stick to plugins in the
  `github-pages` gem's allowlist so the site builds with the native GitHub
  Pages Jekyll build (no GitHub Actions workflow needed). If a feature
  requires a non-whitelisted plugin, either find an allowlisted alternative
  or add a GitHub Actions build workflow deliberately (and document it).
- **Data-driven content**: portfolio entries live in `_data/projects.yml`,
  social links in `_data/social.yml`. New repeatable content of this kind
  should follow the same pattern rather than being hardcoded into templates.
- Keep `GUIDE.md` in sync with any change that affects how the site owner
  enters content or previews/publishes the site.

## Planned future enhancement: interactive JS terminal landing page

The next major iteration is an **interactive terminal landing page**,
balanced with the current robust normal-nav pages:

- The homepage (`/`) becomes a fake shell: a blinking prompt
  (`guest@nishthefish92:~$`) where visitors type commands and see output
  printed below.
- **JS-optional / SEO-safe**: `/about/`, `/blog/`, `/projects/`, and
  individual post pages continue to exist as real static HTML pages (as they
  do now). The terminal is a navigation/display layer on top — it should
  read content already rendered by Jekyll (e.g. via `site.posts` /
  `site.data.projects` serialized to JSON at build time) and link/redirect to
  the real pages, not duplicate content only-in-JS.
- **Core command set** (initial scope, intentionally minimal):
  - `help` — list available commands
  - `ls` — list "files" (about.txt, blog/, projects/, contact.txt)
  - `cat <file>` — print a file's content (e.g. `cat about.txt` shows the bio)
  - `whoami` — one-line intro
  - `blog` — list posts, link to `/blog/`
  - `projects` — list portfolio entries, link to `/projects/`
  - `contact` — print social links from `_data/social.yml`
  - `clear` — clear the terminal output
- Provide a visible fallback (e.g. a small "or browse: about · blog ·
  projects" line) for users who don't want to type commands, and ensure
  keyboard focus / mobile usability are addressed.
- Implementation should be vanilla JS, no framework, kept in
  `assets/js/terminal.js` (or similar), progressively enhancing the existing
  pages rather than replacing them.

This is **not yet implemented** — the current homepage (`index.md`) is a
static styled page. When picking this up, re-read this section and
`GUIDE.md` before changing the page structure, since the guide's
instructions for editing `index.md` / `about.md` / etc. will need to be
updated to match.
