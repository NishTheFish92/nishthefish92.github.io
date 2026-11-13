---
title: "Talking to my lecture slides with NotesAssistant"
date: 2026-11-13 12:00:00 +0000
tags: [rag, langchain, faiss, embeddings, gemini]
---

This post is a quick look at how I built [NotesAssistant](https://github.com/NishTheFish92/NotesAssistant),
a RAG tool that let me ask questions about my lecture slides, and the main
problems I ran into along the way.

## Why I built it

In my fourth semester (2025) I had Computer Architecture and Organization, and
the slides we got were long, usually around 80 pages each. Studying from them
meant working through the numerical problems and also remembering the theory on
every slide.

So I wanted a way to talk to my slides. If I wanted to learn about a topic and
didn't know which slide it was on, I could just ask and get the relevant part
back instead of hunting for it in a huge PPT.

## Problem 1 - Slide text is a mess

Faculty slides make sense when you're looking at them, but copy the text out
and it's hard to tell what's going on. An LLM wouldn't make much sense of it
either.

So instead of extracting the text, I sent an image of each slide to a vision LLM
and had it describe the slide. Those descriptions became the knowledge base.

## Chunking and embedding

I embedded the descriptions with BGE, an open source embedding model that turns
text into vectors of length 768 representing its meaning. The vectors went into
a FAISS index saved locally.

My first chunking strategy was naive and just split the text every few thousand
characters. Later I switched to token aware chunking, measuring chunks with
BGE's own tokenizer, since BGE only reads the first 512 tokens of a chunk and
ignores the rest. For these slides it didn't make a difference to the eval
scores because none of them were that filled with text, but it would matter for
denser documents.

## Problem 2 - Maxing out my VRAM

Embedding the whole knowledge base at once maxed out my 4GB of VRAM. It was slow
and my laptop was getting cooked.

Batching fixed it. I embedded the documents in batches and merged each one into
the index, and VRAM usage dropped to under 1GB. Indexing got a lot faster too.

Not all of that speedup was from batching though. While making the change I
found that the line building the index in my indexing script was inside the loop
over pages, so every page re-embedded all the pages before it. Moving it out of
the loop helped a lot.

## Asking questions

When I asked something, the 4 closest chunks were pulled from the index and
passed to Gemini along with their page numbers. It answered only from those
chunks, cited the pages it used, and said "The notes don't cover this." when the
chunks didn't have the answer. I could use it from the CLI or a Streamlit UI.

## Problem 3 - Gemini's recitation filter

Sending one slide per call meant a lot of LLM calls, so I tried sending a bunch
of slides in one request.

Gemini didn't like that. It has a recitation filter that blocks a response if it
thinks the output is reproducing existing material too closely, and batched
slides kept triggering it. Asking it to describe the slides instead of
transcribing them didn't help either. One slide at a time never set it off, so I
stuck with that. Batching might work with an open source model, but not with
Gemini.

## Checking that it works

To see how well it actually worked, I wrote a golden set of 58 questions on one
deck about I/O organisation. Most had their answer on a single slide, some
needed several slides or were numericals from the slides, and 10 couldn't be
answered from the slides at all, so the right response there was a refusal.

The slide with the answer showed up in the top 4 chunks for 94% of the
answerable questions. I had a different LLM grade the answers and checked its
verdicts by hand. 93% of answers were correct, every answer stuck to the
retrieved chunks, and 89% of the page citations pointed to the right slide. It
only answered 1 of the 10 questions it should have refused. Most of the misses
came from the right slide not being retrieved in the first place.

## Looking back

The project started as a way to get through 80 page slide decks, and most of
what I learned came from the problems along the way, like messy slide text,
running out of VRAM, and Gemini blocking batched requests.
