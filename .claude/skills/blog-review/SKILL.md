---
name: blog-review
description: Proofread a blog post in _posts/ for obvious grammatical errors only — typos, spelling, agreement, punctuation, capitalization. Use when the user asks to proofread, spell-check, or grammar-check a blog post or draft in this repo. Reports errors with corrections; never edits the file, and never comments on tone, style, or structure.
---

# Blog review

Proofread a blog post for **obvious grammatical errors only**. The author
handles tone, style, structure, and flow themselves — do **not** comment on
those. **This skill is report-only — do not edit the post.** List the errors;
the author applies the fixes.

## 1. Pick the target post

- If the user named a file, use it.
- Otherwise list the `.md` files in `_posts/`. If there's exactly one, review
  it. If there are several, show them and ask which (or "all").

Read the file. Separate the YAML front matter (between the `---` lines) from
the body. **Check the body prose only** — never flag front matter, code
fences, fenced/inline code, URLs, or Liquid/HTML tags.

## 2. What to flag — and what NOT to

Flag only clear, objective grammatical mistakes:

- Spelling errors and typos
- Subject–verb agreement (e.g. "decisions **was**" → "**were**")
- Verb-tense mistakes
- Punctuation errors (missing periods, comma splices, capital after a
  semicolon, missing spaces after punctuation)
- Capitalization errors
- Missing or incorrect articles (a/an/the)
- Homophones (its/it's, your/you're, their/there)
- Wrong prepositions and broken fixed idioms (e.g. "at a **crossroad**" →
  "**crossroads**")
- Hyphenation of compound modifiers (e.g. "user **friendly**" →
  "**user-friendly**")

**Do NOT flag** — these belong to the author:

- Tone, voice, formality, or register
- Casual idioms, slang, contractions, or filler words ("really", "just")
- Wordiness, conciseness, or sentence/paragraph rewrites for clarity
- Repeated words, transitions, structure, headings, or flow

If a sentence is grammatically correct, leave it alone even if it could be
phrased better. When in doubt about whether something is an outright error,
omit it.

Do, however, include a single **readability grade** line at the top of the
report — the approximate Flesch–Kincaid grade level — purely as a curiosity
stat. Do not turn it into structure/length feedback or suggest changing it.

## 3. Output format

Print a plain Markdown list to the conversation (do **not** write a file).
Order by line number. For each error give the line, a short quote, and the
correction:

```
# Proofread: <filename>

**Readability:** ~grade <N> (Flesch–Kincaid, for curiosity only)

- **L<line>** "<quote>" → <correction>. <one-clause reason only if not obvious>

<repeat per error>

Found <N> error(s).
```

If there are no errors, say so in one line. End by reminding the author you
can apply any specific fixes they point to — but make no edits unless they
explicitly ask.
