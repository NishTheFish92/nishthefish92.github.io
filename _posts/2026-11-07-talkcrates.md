---
title: "Building TalkCrates, a chat app for talking to yourself"
date: 2026-11-07 12:00:00 +0000
tags: [react, sqlite, privacy, design, introspection]
repo: "https://github.com/NishTheFish92/Talkcrates"
---

This post is about [TalkCrates](https://github.com/NishTheFish92/Talkcrates),
one of my more personal projects, and the reasoning behind how I built it. I'll
keep the technical side pretty basic this time.

TalkCrates is essentially a self chat app that lets you talk to yourself. As
delusional as that sounds, I feel like a lot of people don't look inwards often
enough. Introspecting can help you reach a lot of conclusions and learn a lot about yourself,
something which I feel is a pretty interesting process.
I use it myself, and I feel like a lot of people could benefit from looking inwards more often.

## Why Socratic

The name is pronounced "Talk-cra-teez", like Socrates but with "Talk" instead of
"So". It was made to promote Socratic thought and Socratic questioning.

Socratic questioning is a technique used in Cognitive Behavioral Therapy (CBT).
Instead of taking a thought at face value, you question it and work your way
toward a clearer or healthier one. Most of the time, thoughts are never really confronted, so their
thoughts just stay a murky, half formed pool in their head.

TalkCrates gives that process a place to happen. You split your method of thinking into two personas and
have an actual back and forth with yourself, shown as a normal two person chat,
instead of a one sided monologue. You write both sides yourself. The app's only job is to keep the two voices visually
separate so it's easier to switch between them.

## Designing it on paper first

Honestly, the most enjoyable part of the whole thing for me was the design. I sat
down and hand drew each of the screens, along with which screen goes to what.
Once I had that, I described every screen and my requirements to Claude Design,
and Claude made the wireframe from there.

## Keeping your data on your side

I wanted this to be something anyone could trust. I feel like server side
storage has a lot of downsides, especially if proper security precautions aren't
taken when storing sensitive information. That matters even more here, since
this is meant to be a self journaling app.

So there's no account and no server side database. Everything you write stays in
your browser. If you want a backup, or want to move your chats to another
device, you export them as a zip file and import it wherever you want.

Although, there is persistence and your chats stay on the site as long as your IndexedDB isn't cleared. However, it's always a good idea to export from time to time.

Under the hood the data lives in a SQLite database that runs inside the browser,
using [sql.js](https://sql.js.org/). I went with it for one simple reason, which
was compatibility across all devices.

## A blank notebook

Each conversation is its own thread with two participants, and you pick a role
for each one when you create the thread. By default it's you and a more rational
version of you, but you can type in whatever you want.

I made the roles flexible on purpose. I wanted TalkCrates to be more like a blank
notebook that anyone can use however they like, instead of trying to enforce one
fixed way of using it. You can confront a thinking pattern, argue out a
decision, or just vent.

## The POV swap

You can flip your point of view whenever you want, and the whole thread reflows
to show the conversation from the other persona's side, kind of like looking at
the same chat from the other person's phone.

The animation for this was clear in my head. I wanted a smooth and nice
transition, because ultimately the person you're talking to is yourself. I wouldn't want that switch to be jarring. 
The lines that separate different aspects of you are subtle, so I wanted that
subtlety to carry over to the transition as well. So when you swap, each bubble
slides over to the other side and its colour slowly seeps into the other one
instead of snapping straight across.

## Looking back

I feel like there's a very high entry barrier for people who want to pick up a
book and a pen and journal. My hope is that TalkCrates lowers that barrier a bit
and helps people look inwards and confront their own thinking patterns.
