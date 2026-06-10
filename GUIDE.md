# Guide: How this site works (and how to update it)

This guide assumes you know nothing about Jekyll. By the end, you should be
able to:

- Understand what each file/folder does
- Publish changes by editing files on github.com (no software install)
- Optionally preview the site on your own computer before publishing
- Write blog posts, add portfolio projects, edit your bio, change links, and
  recolor the site

---

## 1. What this is

- **Jekyll** is a tool that turns plain text/Markdown files into a full
  website (HTML, CSS). You write simple `.md` files; Jekyll wraps them in
  the page design (the "layout") and produces a website.
- **GitHub Pages** is a free hosting service built into GitHub. Because this
  repository is named `nishthefish92.github.io`, GitHub automatically:
  1. Watches the default branch (`main`) for changes.
  2. Runs Jekyll to build the site.
  3. Publishes the result at **https://nishthefish92.github.io**.

So the whole workflow is: **edit a file → commit → push (or save on
github.com) → wait ~30-60 seconds → site updates automatically.** There is
no separate "deploy" step and no server to manage.

> **One-time setup**: if you haven't already, enable Pages for this repo:
> go to **Settings → Pages**, and under "Build and deployment" set
> **Source = Deploy from a branch**, branch = `main`, folder = `/ (root)`.
> Save. The first build can take a minute or two.

---

## 2. Map of the project

| Path | What it is | Do you edit it? |
|---|---|---|
| `_config.yml` | Site-wide settings: title, description, author, plugins | Occasionally (site name/description) |
| `index.md` | The home page | Yes — your tagline & intro |
| `about.md` | The `/about/` page | Yes — your full bio |
| `blog.html` | The `/blog/` page (auto-lists all posts) | Rarely |
| `projects.md` | The `/projects/` page (reads `_data/projects.yml`) | Rarely (edit the data file instead) |
| `404.html` | Page shown for broken links | Rarely |
| `_posts/` | Your blog posts, one file per post | **Yes — this is where you write** |
| `_data/projects.yml` | Your portfolio entries (used by `/projects/`) | **Yes** |
| `_data/social.yml` | Links shown in the footer (GitHub, email, etc.) | **Yes** |
| `_layouts/` | Page templates (default page, post page, etc.) | Only for design changes |
| `_includes/` | Reusable HTML snippets (nav bar, footer, `<head>`) | Only for design changes |
| `assets/css/style.css` | All styling, including the color theme | Yes — to recolor |
| `Gemfile` | Lists the Ruby tools needed for local preview | No |
| `GUIDE.md` | This file | No |
| `CLAUDE.md` | Notes for future development (not shown on the site) | No |

**The short version**: 90% of the time, you'll only touch `_posts/`,
`_data/projects.yml`, `_data/social.yml`, `about.md`, and `index.md`.

---

## 3. Publishing — editing directly on github.com

You don't need to install anything to update content.

1. Go to the repository on github.com.
2. Open the file you want to change (e.g. `about.md`).
3. Click the **pencil icon** ("Edit this file") in the top-right of the file
   view.
4. Make your changes.
5. Scroll down to "Commit changes", add a short message describing what you
   changed, and click **Commit changes** (this commits directly to `main`).
6. Wait about 30-60 seconds, then check
   **https://nishthefish92.github.io** — your change should be live.

**To create a new file** (e.g. a new blog post): inside the `_posts/`
folder on github.com, click **Add file → Create new file**, type the
filename (see the blog post format below), paste your content, and commit.

**Watching the build**: click the **Actions** tab in the repo. Each commit
triggers a "pages build and deployment" run. A green check means it
published successfully; a red X means something went wrong (see
Troubleshooting).

---

## 4. One-time local setup (optional, for previewing before you publish)

This lets you see your changes on your own computer at
`http://localhost:4000` before committing.

1. **Install Ruby** (version 3.1+ recommended).
   - macOS: `brew install ruby`
   - Linux (Debian/Ubuntu): `sudo apt install ruby-full build-essential`
   - Windows: use [RubyInstaller](https://rubyinstaller.org/) (pick the
     "with DevKit" version)
2. **Install Bundler** (manages Ruby gems/dependencies):
   ```
   gem install bundler
   ```
3. **Install this site's dependencies**. From inside the project folder:
   ```
   bundle install
   ```
   This reads the `Gemfile` and installs Jekyll plus the GitHub Pages
   plugins, so your local build matches what GitHub will produce.

You only need to do this once per computer (re-run `bundle install` if
`Gemfile` ever changes).

---

## 5. Preview locally

From the project folder, run:

```
bundle exec jekyll serve
```

- Open **http://localhost:4000** in your browser.
- The terminal window will stay open and watch for file changes — most edits
  (posts, pages, data files) automatically rebuild and you just need to
  refresh the browser.
- **Exception**: if you edit `_config.yml`, stop the server (`Ctrl+C`) and
  run `bundle exec jekyll serve` again — config changes require a restart.
- To stop the server, press `Ctrl+C` in the terminal.

Once you're happy with how it looks locally, commit and push your changes
(or edit on github.com as in section 3) to publish.

---

## 6. Entering your data

### 6.1 Edit your bio

- **Short version (home page)**: open `index.md`. Replace the two
  `PLACEHOLDER` paragraphs in the `hero` section with your real tagline and
  a short intro.
- **Full bio**: open `about.md` and replace the placeholder headings/bullets
  with your real background, what you do, your skills, etc. It's plain
  Markdown — see the cheat sheet below.

### 6.2 Write a blog post

1. In the `_posts/` folder, create a new file named:
   ```
   YYYY-MM-DD-short-title.md
   ```
   Example: `2026-07-01-learning-rust.md`. The date **must** be in this
   format and matches (or predates) today — Jekyll uses it for sorting and
   the post URL.
2. Start the file with **front matter** (the part between the `---` lines),
   then your content in Markdown below it:

   ```markdown
   ---
   title: "Learning Rust"
   date: 2026-07-01 09:00:00 +0000
   tags: [rust, learning]
   ---

   Today I started learning Rust. Here's what I've found so far...
   ```

   - `title` — shown as the page title and in post listings.
   - `date` — controls ordering and the displayed date. Must match or be
     earlier than the current date — **future-dated posts won't appear**
     until that date arrives (or until built locally with the
     `--future` flag: `bundle exec jekyll serve --future`).
   - `tags` — optional list of short labels, shown under the title.

3. Save (or commit). The post automatically appears on the home page
   ("recent posts") and on `/blog/` — you don't need to edit any other file.

Use `_posts/2026-06-10-welcome.md` as a working example/template — copy it
and edit.

### 6.3 Add a portfolio project

Open `_data/projects.yml`. Each project is a list entry like:

```yaml
- name: "My Cool App"
  description: "A web app that does X, built because Y."
  tech:
    - "Python"
    - "React"
  url: "https://my-cool-app.example.com"
  repo: "https://github.com/yourusername/my-cool-app"
```

- `name` and `description` are required.
- `tech`, `url`, and `repo` are optional — omit a line if it doesn't apply
  (e.g. no live demo? remove the `url` line).
- Add as many entries as you like; each becomes a card on `/projects/`.
- Delete the two example entries once you've added your real projects.

**Important**: this is YAML, so indentation matters. Copy an existing entry
and edit the values rather than typing one from scratch.

### 6.4 Add or change social/contact links

Open `_data/social.yml`:

```yaml
- name: "github"
  url: "https://github.com/nishthefish92"
- name: "email"
  url: "mailto:you@example.com"
```

- `name` is the label shown in brackets in the footer (e.g. `[github]`).
- `url` is where it links. For email, use `mailto:address@example.com`.
- Add, remove, or reorder entries freely.

### 6.5 Change the site name/description

Open `_config.yml` and edit:

```yaml
title: "nishthefish92"
description: "Personal site, blog, and portfolio..."
author: "Nishant"
```

- `title` appears in the browser tab, the terminal window title bar, and the
  nav prompt (`guest@<title>: ~`).
- Remember: changes to `_config.yml` require restarting `jekyll serve` to
  see locally (no restart needed when published via GitHub Pages).

### 6.6 Recolor the theme

Open `assets/css/style.css` and look at the `:root { ... }` block at the
very top — every color in the site is defined there:

```css
:root {
  --bg: #1a1b26;        /* page background */
  --fg: #c0caf5;        /* main text color */
  --accent: #7dcfff;    /* links, highlights */
  --accent-2: #bb9af7;  /* secondary accent */
  --green: #9ece6a;
  --yellow: #e0af68;
  --red: #f7768e;
  --border: #292e42;
  ...
}
```

Change any hex value and the whole site updates — no need to hunt through
other files. For example, for a classic green-on-black terminal look, try:

```css
--bg: #0a0a0a;
--fg: #33ff66;
--accent: #33ff66;
--muted: #1f7a36;
--border: #1f3d27;
```

---

## 7. Markdown + front matter cheat sheet

```markdown
# Heading 1
## Heading 2
### Heading 3

**bold text**
*italic text*

[link text](https://example.com)

![image alt text](/assets/images/photo.jpg)

- bullet item
- another item

1. numbered item
2. another item

> a quoted block

`inline code`

```python
# a fenced code block with syntax highlighting
print("hello")
```

---  (a horizontal rule / divider)
```

**Front matter** is the block at the top of `.md` files between two `---`
lines. It's written in YAML and sets metadata Jekyll uses (title, date,
layout, tags, etc.). Every page and post needs it, even if it's just:

```yaml
---
title: My Page
---
```

---

## 8. Troubleshooting

**My new post isn't showing up**
- Check the filename format: `YYYY-MM-DD-title.md` in `_posts/`.
- Check the `date:` in front matter isn't in the future (or use
  `--future` when running locally).
- Check the front matter starts and ends with `---` on its own line.

**The live site didn't update after I committed**
- Go to the **Actions** tab and check the latest "pages build and
  deployment" run. A red X means the build failed — click into it to see
  the error (often a YAML syntax mistake in `_config.yml` or a `_data/*.yml`
  file, or broken front matter in a post).
- Give it a minute — builds aren't instant.

**I changed `_config.yml` but nothing happened locally**
- Stop (`Ctrl+C`) and restart `bundle exec jekyll serve`. Config changes
  need a restart; content changes don't.

**A page looks broken / unstyled**
- Make sure the file's front matter has the opening and closing `---` lines
  and a `layout:` value (or that it's a type that gets a default layout —
  see `_config.yml`'s `defaults` section).

**`bundle install` or `jekyll serve` fails**
- Make sure Ruby and Bundler are installed (section 4) and that you're
  running the command from inside the project folder (the one containing
  `Gemfile`).

**YAML errors in `_data/projects.yml` or `_data/social.yml`**
- YAML is picky about indentation (use spaces, not tabs) and requires a
  space after each colon (`name: "value"`, not `name:"value"`). Copy an
  existing entry's structure exactly when adding a new one.
