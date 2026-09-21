# CLAUDE.md

This repo is a finished Jekyll blog (`nishthefish92.github.io`). The main use of
Claude Code here is writing tech blog posts in `_posts/`. For how the site is
built, see `CODEBASE.md`. For the post file format, see `GUIDE.md`.

## Blog writing workflow

When the user wants to write a tech blog, this section applies. The job is to
turn their loose thoughts into a post that sounds like them, and to make sure
nothing wrong ends up published. Do not write the post until the user says the
ideas are in sync.

### How a session goes

1. **User dumps content.** It arrives as a rambly paragraph, usually speech to
   text. Expect typos, misheard words (especially tech terms and product names),
   missing punctuation, and half-finished thoughts. Quietly fix the obvious ones
   from context. If a term could be two different things, ask.
2. **Play it back.** Summarize what you understood in a few plain bullets or a
   short paragraph: the main point, the story or steps, and the takeaway. Keep
   it short. The goal is for the user to see whether we're on the same page.
3. **Flag anything shaky.** Call out claims that look wrong, oversimplified, or
   unverifiable, and say why. The user does not want to publish misinformation,
   so this is the most important step. If a fact is checkable, check it (docs,
   the code in this repo, a quick search) instead of just asking. Never fill a
   gap with a confident guess. Say "I'm not sure about this part" and ask.
4. **Ask sparingly.** A few targeted questions at a time, not a questionnaire.
   Prefer questions that fill real gaps (what actually happened, why they chose
   X, what went wrong) over ones that just seek reassurance.
5. **Repeat** until the user says the ideas are settled. Only then draft.
6. **Draft** into `_posts/YYYY-MM-DD-short-title.md` (see the format in
   `GUIDE.md` and the existing post for reference). Use today's date. Show the
   user the draft and take edits. Don't publish or commit unless asked.
7. When the draft is done, offer to run the `blog-review` skill for a
   typo and grammar pass.

### Content rules

- **Only the user's ideas.** Everything in the post must come from what they
  said or from things we verified together. Don't add extra claims, stats,
  benchmarks, comparisons, or "best practices" they didn't mention. If a
  section feels thin, ask them for more instead of padding it.
- **Keep their opinions as opinions.** If they say "I found X easier", write it
  that way. Don't upgrade it to "X is easier".
- **Code and commands must be real.** Only include snippets the user gave or we
  ran and confirmed. Don't invent output.
- **Links.** Add a link the first time a tool, product, or company comes up,
  but only if you're sure of the URL.

### Voice

Write the way the user talks when explaining something out loud, like they're
walking a friend through it or narrating a video script. Natural and free
flowing, not stiff or textbook-y.

- First person, direct. "I", "we", "you" are all fine.
- Contractions are good. "It's", "didn't", "we'll".
- Short sentences mixed with longer ones. Start sentences with "So", "But",
  "And", "Now" when it feels right.
- Explain things the way you'd say them, then name the technical term. Prefer
  "the server just kept dropping the connection" over "the server exhibited
  intermittent connection instability".
- Keep the user's own phrasing and quirks where they work. Clean up only what
  the speech to text mangled.
- Avoid corporate and AI-sounding filler: "delve", "leverage", "robust",
  "seamless", "in today's fast-paced world", "it's important to note",
  "let's dive in", "in conclusion". No cheesy intro or wrap-up paragraphs.
- Don't over-format. Some headers to break up sections are fine (the existing
  post uses `##` headings), but don't turn everything into bullet lists or
  bolded buzzwords. Write in paragraphs the way you'd talk.
- Lead with the point or the problem, not with throat clearing.
- Match the length to the content. A short post is fine.

### Hard rule: no em dashes

Never use em dashes (the long dash character) or `--` as a stand in, in the blog
content or in the chat while working on it. Use a comma, a period, parentheses,
or just reword the sentence. Check the final draft for them before showing it.
