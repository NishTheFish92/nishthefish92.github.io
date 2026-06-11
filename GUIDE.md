# Guide: entering data

How to add and edit content on the site. This assumes you're comfortable with
Markdown — it focuses on the project-specific bits (where things live, what
fields exist, and the gotchas). For how the whole thing is wired together, see
`CODEBASE.md`.

**The 5 files you'll actually touch:** `index.md`, `about.md`, `_posts/*`,
`_data/projects.yml`, `_data/social.yml`. Everything else is structure.

---

## Workflow: edit → preview → publish

**Publish (the only required step):** commit a change to the `main` branch —
either edit on github.com (pencil icon → Commit changes) or push from your
machine. GitHub rebuilds and the live site updates in ~30–60s. No deploy step.

> First time only: enable hosting at repo **Settings → Pages** →
> Source = *Deploy from a branch* → branch `main`, folder `/ (root)`.

**Preview locally (optional, recommended before publishing):**

```bash
bundle install        # one-time, installs Jekyll + plugins from the Gemfile
bundle exec jekyll serve   # serves http://localhost:4000, auto-rebuilds on save
```

Refresh the browser to see edits. Two exceptions: editing `_config.yml`
requires restarting the server, and future-dated posts need
`bundle exec jekyll serve --future`. (If Ruby isn't installed:
`sudo apt install ruby-full build-essential && gem install bundler`.)

---

## Edit your bio

- **Home page intro** — `index.md`: replace the two `PLACEHOLDER` paragraphs
  in the `<section class="hero">` block.
- **Full bio** — `about.md`: replace the placeholder content. Plain Markdown;
  the page heading is added automatically.

---

## Write a blog post

Create a file in `_posts/` named `YYYY-MM-DD-short-title.md`:

```markdown
---
title: "Learning Rust"
date: 2026-07-01 09:00:00 +0000
tags: [rust, learning]
---

Your post body in Markdown...
```

- **Filename date and `date:`** should agree. The filename date + the
  `-short-title` slug determine the URL → `/blog/short-title/`.
- `title` — shown in listings and as the page heading.
- `date` — controls ordering and the displayed date. A date **in the future**
  hides the post until that day (use `--future` to preview early).
- `tags` — optional list, rendered as `#tag` chips.

It auto-appears on the home page (newest 5) and `/blog/` (all). Copy
`_posts/2026-06-10-welcome.md` as a starting template.

---

## Add a portfolio project

Edit `_data/projects.yml`. Each list entry becomes a card on `/projects/`:

```yaml
- name: "My Cool App"            # required
  description: "What it does."   # required
  tech:                          # optional list of chips
    - "Python"
    - "React"
  url: "https://demo.example.com"          # optional — links the title
  repo: "https://github.com/you/cool-app"  # optional — adds a [source] link
```

Omit any optional line you don't need. It's YAML, so indentation matters
(spaces, not tabs) — copy an existing entry rather than typing from scratch.
Delete the two example entries once you've added your own.

---

## Edit social / footer links

Edit `_data/social.yml`. Each entry is a bracketed link in the footer:

```yaml
- name: "github"                          # label shown as [github]
  url: "https://github.com/nishthefish92"
- name: "email"
  url: "mailto:you@example.com"           # use mailto: for email
```

Add, remove, or reorder freely.

---

## Site name / description

Edit `_config.yml`:

```yaml
title: "nishthefish92"      # browser tab, terminal titlebar, nav prompt
description: "..."          # used by SEO/social meta tags
author: "Nishant"           # footer copyright
```

Changes here require a server restart to show locally.

---

## Theme (colors + font)

All in the `:root { ... }` block at the top of `assets/css/style.css` — change
a value, the whole site updates.

```css
--bg: #0d0d13;        /* page background */
--bg-alt: #0a0a0d;    /* panels (terminal titlebar, cards) */
--fg: #c0caf5;        /* main text */
--accent: #7dcfff;    /* links / highlights */
/* ...plus --accent-2, --green, --yellow, --red, --border */
```

**Font:** `--font-mono` leads with "VCR OSD Mono" (file at
`assets/fonts/VCR_OSD_MONO.ttf`), falling back to JetBrains Mono. To swap
fonts, see `CODEBASE.md` §8.

---

## Troubleshooting

| Symptom | Fix |
|---|---|
| New post not showing | Filename must be `YYYY-MM-DD-title.md`; `date:` not in the future (or use `--future`); front matter fenced by `---` top and bottom. |
| Live site didn't update | Check repo **Actions** tab — a red ✗ means the build failed (usually a YAML typo). Builds aren't instant. |
| `_config.yml` change not visible locally | Restart `bundle exec jekyll serve` (only this file needs a restart). |
| Page renders unstyled / raw | Front matter missing its opening/closing `---` lines. |
| YAML error in `_data/*` | Use spaces not tabs; space after each colon (`name: "x"`). Copy a working entry's structure. |
| `bundle`/`jekyll` command fails | Ruby + Bundler installed? Run from the folder containing `Gemfile`. |
