# Codebase walkthrough

A complete, file-by-file explanation of how this site is built and wired
together. Read this to understand *what's going on* under the hood. For the
day-to-day "how do I add content" steps, see `GUIDE.md`.

---

## 1. The mental model

This is a **static site**: there is no server running your code, no database,
no backend. Everything a visitor sees is plain HTML/CSS files generated ahead
of time. Two pieces produce those files:

- **Jekyll** — a build tool. It reads the source files in this repo
  (Markdown, HTML templates, YAML data) and outputs a finished website into a
  folder called `_site/`.
- **GitHub Pages** — the host. Because the repo is named
  `nishthefish92.github.io`, GitHub runs Jekyll for you on every push to the
  `main` branch and serves the resulting `_site/` at
  `https://nishthefish92.github.io`.

So the lifecycle of any change is:

```
edit a source file  →  commit/push to main  →  GitHub runs Jekyll
                     →  _site/ is regenerated →  served at the live URL
```

`_site/` is the build output. It's listed in `.gitignore` and never committed
— it's disposable and regenerated every build. You only ever edit *source*
files.

---

## 2. How Jekyll turns source into pages

Three mechanisms do almost all the work. You'll see them everywhere in the
files, so it's worth knowing them before the file-by-file tour.

### a. Front matter

Any file that starts with a block fenced by `---` lines is processed by
Jekyll. That block is **front matter** — YAML key/values that configure the
page. Example from `index.md`:

```yaml
---
layout: default
title: Home
permalink: /
---
```

- `layout` — which template in `_layouts/` to wrap this page in.
- `title` — used in the `<title>` tag and headings.
- `permalink` — the URL this page is published at.

A file with no front matter is copied to the output untouched (this is why
`assets/css/style.css` is plain CSS but still gets served).

### b. Liquid — the templating language

Inside templates and pages you'll see two kinds of Liquid markup:

- `{{ ... }}` — **output**: prints a value. e.g. `{{ site.title }}` prints
  `nishthefish92`.
- `{% ... %}` — **tags**: logic. e.g. `{% if %}`, `{% for %}`, `{% include %}`.

Values come from a few objects:

- `site` — anything in `_config.yml` (`site.title`, `site.author`) plus
  `site.posts` (all blog posts) and `site.data` (your YAML data files).
- `page` — the current page's front matter (`page.title`, `page.url`,
  `page.date`, `page.tags`).
- `content` — the rendered body of the page, injected into a layout.

Values can be piped through **filters** with `|`. The ones used in this repo:

| Filter | What it does | Used in |
|---|---|---|
| `relative_url` | Prefixes a path with the site's base URL | every link/asset |
| `date: "%Y-%m-%d"` | Formats a date | post lists, post meta |
| `date_to_xmlschema` | ISO 8601 date for the `<time>` tag | `_layouts/post.html` |
| `strip_html` | Removes HTML tags | blog excerpts |
| `truncatewords: 25` | Cuts text to 25 words | blog excerpts |

### c. Layouts and includes

- **Layouts** (`_layouts/`) are page templates. A page declares
  `layout: page`; Jekyll renders the page's body and drops it into that
  layout where `{{ content }}` appears. Layouts can themselves have a layout,
  forming a chain (see §4).
- **Includes** (`_includes/`) are reusable HTML snippets pulled in with
  `{% include head.html %}`. Used for the parts repeated on every page (the
  `<head>`, nav bar, footer).

---

## 3. `_config.yml` — site-wide settings

Read once at the start of every build. Key entries:

```yaml
title / description / author / url    # available everywhere as site.<key>
baseurl: ""                           # empty because this is a root-level user site
markdown: kramdown                    # the Markdown → HTML engine
permalink: /blog/:title/              # URL pattern for blog posts
```

- **`permalink: /blog/:title/`** is why a post file
  `2026-06-10-welcome.md` is published at `/blog/welcome/` (the date is
  stripped, `:title` comes from the filename). Change this pattern and every
  post URL changes.

- **`plugins:`** lists three plugins, all on GitHub Pages' allowlist (so no
  custom build setup is needed):
  - `jekyll-feed` → generates `/feed.xml` (RSS) from your posts.
  - `jekyll-seo-tag` → the `{% seo %}` tag in `head.html` expands into
    `<title>`, Open Graph, and other meta tags.
  - `jekyll-sitemap` → generates `/sitemap.xml`.

- **`defaults:`** sets front matter automatically so you don't repeat it:
  files of type `posts` get `layout: post`, and `pages` get `layout: page`.
  (Pages still override this when needed — e.g. `index.md` and `404.html`
  explicitly set `layout: default`.)

- **`exclude:`** lists files Jekyll should *not* publish: `Gemfile`,
  the docs (`GUIDE.md`, `CODEBASE.md`, `CLAUDE.md`, both `README.md`s).
  Without this, the `jekyll-readme-index` plugin (bundled with GitHub Pages)
  would turn `README.md` files into public pages.

---

## 4. The layout chain (`_layouts/`)

Every page funnels through these. The chain is `page`/`post` → `default`.

### `default.html` — the outer shell (used by every page)

```html
<html> <head>{% include head.html %}</head>
<body>
  <div class="crt">              ← CRT scanline overlay wrapper
    <div class="container">      ← centered, max-width column
      {% include nav.html %}     ← terminal-window header + nav
      <main>{{ content }}</main> ← the page's content goes here
      {% include footer.html %}  ← social links + copyright
    </div>
  </div>
</body></html>
```

This is the only file with `<html>`/`<body>`. Everything else renders *into*
its `{{ content }}`.

### `page.html` — generic content pages

Declares `layout: default`, then adds an `<h1>` from the page's `title` and
wraps the body:

```html
<article class="page">
  <h1 class="page__title">{{ page.title }}</h1>
  <div class="page__content">{{ content }}</div>
</article>
```

Used by `about.md`, `blog.html`, `projects.md` (via the `defaults` rule).
This is why those pages automatically get a heading matching their `title`.

### `post.html` — individual blog posts

Also `layout: default`. Renders the post title, a `<time>` element (machine
date via `date_to_xmlschema`, human date via `date: "%Y-%m-%d"`), the tag
list, the body, and a "back to /blog" link:

```html
<time datetime="{{ page.date | date_to_xmlschema }}">{{ page.date | date: "%Y-%m-%d" }}</time>
{% if page.tags.size > 0 %} ... {% for tag in page.tags %}<span class="tag">#{{ tag }}</span>{% endfor %} ... {% endif %}
```

So: a post file gets `layout: post` (from `defaults`) → renders into
`post.html` → which renders into `default.html`.

---

## 5. The includes (`_includes/`)

### `head.html` — everything inside `<head>`

- Builds the `<title>`: `"<page title> :: <site title>"`, or just the site
  title on pages without one.
- Loads the **JetBrains Mono** webfont from Google Fonts (the fallback font —
  see §8).
- Links the stylesheet and the RSS feed.
- `{% seo %}` — expands (via `jekyll-seo-tag`) into SEO/social meta tags.

### `nav.html` — the terminal-window header

Renders the fake terminal chrome and the nav:

```
┌ guest@nishthefish92: ~              ● ● ●  ┐   ← titlebar: label left, dots right
│ > ~/   > about   > blog   > projects │       ← nav links
```

The three colored dots (`term-dot--red/yellow/green`) are purely decorative.
Each nav link adds an `nav-link--active` class when it matches the current
page, using `page.url`:

```liquid
class="nav-link{% if page.url == '/about/' %} nav-link--active{% endif %}"
```

Blog uses `{% if page.url contains '/blog' %}` so the tab stays highlighted on
individual post pages too.

### `footer.html` — social links + copyright

Loops your social entries into bracketed links, then prints the year and
author with a blinking cursor:

```liquid
{% for item in site.data.social %}<a href="{{ item.url }}" ...>[{{ item.name }}]</a>...{% endfor %}
&copy; {{ site.time | date: '%Y' }} {{ site.author }} — built with Jekyll
```

`site.time` is the build time, so the copyright year is always current.

---

## 6. The pages

### `index.md` — home (`/`)

Uses `layout: default` directly (not `page`), so it has **no** auto `<h1>` —
it builds its own "terminal prompt" hero instead. Two parts:

1. **Hero** — fake prompt lines (`guest@... :~$ whoami`) with PLACEHOLDER
   tagline/intro text.
2. **Recent posts** — loops the 5 newest posts:
   ```liquid
   {% if site.posts.size > 0 %}
     {% for post in site.posts limit: 5 %} ... {{ post.date | date: "%Y-%m-%d" }} ... {{ post.title }} ...
   {% else %} No posts yet ... {% endif %}
   ```
   `site.posts` is automatically sorted newest-first.

### `blog.html` — post index (`/blog/`)

`layout: page`. Loops **all** `site.posts` (no limit), showing date, title,
and an excerpt (`post.excerpt | strip_html | truncatewords: 25`). The excerpt
is, by default, the post's first paragraph.

### `projects.md` — portfolio (`/projects/`)

`layout: page`. Loops `site.data.projects` (the `_data/projects.yml` file).
For each entry it renders a card and conditionally shows pieces only if
present:

```liquid
{% if project.url %}<a href="{{ project.url }}">{{ project.name }}</a>{% else %}{{ project.name }}{% endif %}
{% if project.tech %} ... {% for t in project.tech %}<span class="tag">{{ t }}</span>{% endfor %} ... {% endif %}
{% if project.repo %} ... [source] ... {% endif %}
```

This is the key "data-driven" page: you never edit `projects.md`, you edit the
YAML it reads.

### `about.md` — bio (`/about/`)

`layout: page`. Plain Markdown content; the heading comes from the layout.

### `404.html` — not-found page

`layout: default`, `permalink: /404.html`. GitHub Pages automatically serves
this file for any URL that doesn't exist. It's static, so it can't know which
URL the visitor typed — the `cd ./that-page` line is deliberately generic.

---

## 7. Content data

### `_posts/` — blog posts

- Filename **must** be `YYYY-MM-DD-title.md`. Jekyll parses the date from the
  filename and the slug becomes the `:title` in the permalink.
- Front matter sets `title`, `date`, `tags`. `layout: post` is applied
  automatically by `_config.yml`'s `defaults`.
- A **future-dated** post is excluded from the build unless Jekyll is run with
  `--future`. This is the most common "my post isn't showing" cause.

### `_data/` — structured data

Any `.yml` file here is exposed as `site.data.<filename>`:

- `_data/projects.yml` → `site.data.projects` → consumed by `projects.md`.
  Each entry: `name`, `description` (required), `tech` (list), `url`, `repo`
  (optional).
- `_data/social.yml` → `site.data.social` → consumed by `footer.html`.
  Each entry: `name` (label), `url`.

This is the clean way to manage repeating content: the template defines the
*shape* once, the YAML holds the *data*.

---

## 8. Styling (`assets/css/style.css`)

One stylesheet, organized top-to-bottom. The important structural ideas:

### Theme variables (`:root`)

Every color and the font are CSS custom properties at the very top:

```css
:root {
  --bg / --bg-alt / --fg / --muted / --accent / --accent-2
  --green / --yellow / --red / --border
  --font-mono: "VCR OSD Mono", "JetBrains Mono", ... monospace;
  --max-width: 760px;
}
```

Every rule below references these (e.g. `color: var(--fg)`), so recoloring or
re-fonting the whole site means editing only this block.

### The font (`@font-face` + fallback chain)

```css
@font-face {
  font-family: "VCR OSD Mono";
  src: url("/assets/fonts/VCR_OSD_MONO.ttf") format("truetype");
}
```

`--font-mono` lists `"VCR OSD Mono"` first, then `"JetBrains Mono"` (loaded in
`head.html`), then system monospace fonts. If the `.ttf` is missing the
browser silently falls to the next font. **Note:** list only formats that
actually exist in `@font-face` — referencing a missing `.woff2` can stop some
browsers from falling through to the `.ttf`.

### The CRT effect

`.crt::before` is a `position: fixed` overlay with a repeating 1px-striped
gradient, giving subtle scanlines over the whole viewport. `pointer-events:
none` keeps it from blocking clicks.

### Layout & components

- `.container` — centers content at `--max-width` (760px).
- `.term-window` — the bordered terminal box (titlebar + nav) from `nav.html`.
- Class naming follows a loose **BEM** convention: `block`,
  `block__element`, `block--modifier` (e.g. `post-list`, `post-list__item`,
  `nav-link--active`). Knowing this makes the selectors predictable.
- Components map 1:1 to the markup: `.hero`, `.post-list`, `.project-card`,
  `.site-footer`, `.error-page`, `.tag`.
- `@keyframes blink` drives the footer cursor.
- One `@media (max-width: 480px)` block handles mobile tweaks.

---

## 9. Tooling files

- **`Gemfile`** — declares the `github-pages` gem (pins Jekyll + plugins to
  the exact versions GitHub Pages runs, so local builds match production) and
  `webrick` (needed to run the local preview server on Ruby 3+).
- **`Gemfile.lock`** — auto-generated exact dependency versions. Gitignored
  here; regenerated by `bundle install`.
- **`.gitignore`** — keeps build output and local install dirs out of git:
  `_site/`, `.jekyll-cache/`, `vendor/`, `Gemfile.lock`, etc.
- **`CLAUDE.md`** — notes for future development (conventions to preserve, the
  planned interactive-terminal feature). Not published.

---

## 10. End-to-end: what produces `/blog/welcome/`

Tracing one URL ties it all together:

1. `_posts/2026-06-10-welcome.md` exists. Its filename gives Jekyll the date
   (`2026-06-10`) and slug (`welcome`).
2. `_config.yml` `permalink: /blog/:title/` → the page is published at
   `/blog/welcome/`.
3. `defaults` assigns `layout: post`.
4. Jekyll renders the Markdown body to HTML, drops it into `post.html`'s
   `{{ content }}` (adding the title, date, tags, back-link).
5. `post.html` declares `layout: default`, so that result is dropped into
   `default.html`'s `{{ content }}` (adding `<head>`, nav, footer).
6. `head.html`, `nav.html`, `footer.html` are pulled in; `{% seo %}` and the
   feed link resolve.
7. The finished HTML is written to `_site/blog/welcome/index.html`.
8. The post also appears in the loops on `index.md` (newest 5) and `blog.html`
   (all), and as an entry in `feed.xml` and `sitemap.xml`.

That same flow, with different layouts and loops, produces every page on the
site.
