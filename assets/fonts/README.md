# Fonts

The site's font stack starts with **"VCR OSD Mono"** (see `--font-mono` in
`assets/css/style.css`). It isn't on Google Fonts and can't be bundled here
directly, so it falls back to "JetBrains Mono" until you add it yourself.

## To enable VCR OSD Mono

1. Download the font (search for "VCR OSD Mono font download" — it's a
   widely distributed freeware font).
2. Place the file in this folder (`assets/fonts/`) named `VCR_OSD_MONO.ttf`.
3. Commit the file. The `@font-face` rule in `assets/css/style.css` already
   points at this path — no other changes needed.

If you skip this, the site still works fine and just uses "JetBrains Mono"
instead.
