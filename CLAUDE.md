# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with
code in this repository.

Guidance for future development sessions on this repo.

## Commands

Dependencies are managed with Bundler (`Gemfile` pins the `github-pages` gem so
local builds match GitHub Pages exactly).

- `bundle install` — install Ruby gem dependencies (first-time setup).
- `bundle exec jekyll serve --watch` — preview at http://localhost:4000 with
  live rebuild on file changes. Always preview over `http://`, not by opening
  `_site/*.html` as a `file://` URL — the CSS/links use absolute paths
  (`/assets/...`) that only resolve when served.
- `bundle exec jekyll build` — one-off build into `_site/` (what GitHub Pages
  runs on push; no manual deploy step).

There is no test/lint suite — this is a static content site. "Verifying" a
change means building and viewing the served page. Note `jekyll serve` without
`--watch` does **not** auto-rebuild, so a stale server is a common cause of
"my change isn't showing."

## What this is

A Jekyll site for the GitHub Pages **user site** `nishthefish92.github.io`
(served at `https://nishthefish92.github.io`). It's a personal
blog/portfolio with a retro CLI/terminal aesthetic.

Two owner-facing docs exist: `CODEBASE.md` (a detailed file-by-file
walkthrough of how the site is built) and `GUIDE.md` (how to enter content —
bio, posts, projects, links, theme). This file is for whoever is writing
code/templates for the site.

## Current approach: terminal-styled, normal navigation

The site *looks* like a terminal window (`_includes/nav.html` renders a
title-bar-style box with red/yellow/green dots and a prompt-style nav), but
navigation is plain `<a>` links to the real content pages, plus a tab that
opens the resume PDF (`assets/Resume.pdf`) in a new tab. No JavaScript is
required for the site to be fully usable or indexable.

## Conventions to preserve

- **Color palette as CSS variables**: all theme colors live in `:root` at the
  top of `assets/css/style.css` (`--bg`, `--fg`, `--accent`, etc.). Any new
  styles should reference these variables, not hardcoded hex values, so the
  whole site can be recolored from one place.
- **Font as a CSS variable with safe fallback**: `--font-mono` leads with
  "VCR OSD Mono", loaded via a local `@font-face` pointing at
  `assets/fonts/VCR_OSD_MONO.ttf` (now present in the repo — see
  `assets/fonts/README.md`). If that file is ever removed, the browser falls
  back to "JetBrains Mono" (loaded via Google Fonts in `_includes/head.html`)
  with no errors. Keep this fallback chain intact when changing fonts. Only
  list one `src` per format in `@font-face` — listing a format whose file
  doesn't exist can prevent fallback to the next `src` in some browsers.
- **GitHub-Pages-supported plugins only**: `_config.yml` lists
  `jekyll-feed`, `jekyll-seo-tag`, `jekyll-sitemap`. Stick to plugins in the
  `github-pages` gem's allowlist so the site builds with the native GitHub
  Pages Jekyll build (no GitHub Actions workflow needed). If a feature
  requires a non-whitelisted plugin, either find an allowlisted alternative
  or add a GitHub Actions build workflow deliberately (and document it).
- **Data-driven content**: portfolio entries live in `_data/projects.yml`,
  social links in `_data/social.yml`. New repeatable content of this kind
  should follow the same pattern rather than being hardcoded into templates.
- Keep `GUIDE.md` in sync with any change to how content is entered or the
  site is previewed/published, and `CODEBASE.md` in sync with any structural
  change (layouts, includes, config, build pipeline, CSS architecture).

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
