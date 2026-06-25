---
title: "Requirement Gathering: From Pain Points to a Spec"
date: 2026-06-10 12:00:00 +0000
tags: [internship, Health Vectors, requirements]
---

This is the first in a series of posts about my summer internship at Health
Vectors. Each one covers a different slice of the build. The stack and the
actual implementation come in later posts. This one is only about requirement
gathering, and the two things it taught me: how to filter, and how to adapt.

## The brief

[Health Vectors](https://www.healthvectors.ai/) is a B2B company that provides
personalized healthcare analytics, delivered through diagnostic labs, hospitals,
clinics, and insurance companies. The problem I was given was deliberately
open-ended:

> The CEO is the primary user, and his expertise is on the business and sales
> side rather than in engineering. His client and lead base is growing fast and
> he can no longer keep up with the deliverables owed to each customer.

Requirement gathering, design, and implementation were all left to me, with the
CTO reviewing and signing off on each cycle. The gathering ran over about a week
and a half, and most of it was listening.

## Collecting everything

The goal early on was simple: understand where his time was going and why
keeping up had become hard. We asked him to list every problem he ran into, big
or small, regardless of whether it seemed worth solving. That was on purpose.
Since his focus is the business and sales side, a lot of what came back was
framed as things that would be nice to have, and that was fine. The more he
listed, the more we had to work from. You cannot filter a list you never
collected.

## Learning one: filtering

The first big thing I learnt was how to filter. Once we had a wide pool of
problems, the real work was deciding what to build now, what to defer, and what
was not worth doing at all. The aim was to find the few items that would have the
most impact and base the product on those.

This part was collaborative, and I want to be honest about my role in it. I was
an observer, a learner, and a participant all at once. I watched how the
filtering was handled, took part in it, and over that week and a half I picked
up why it matters rather than mastering it outright. The pattern was to condense each
problem down to its root form, and once you do that, a long list of separate
problems often collapses into a single underlying requirement. Once you have the real
needs, you break them into smaller chunks so you can tackle one at a time in an iterative manner instead of
trying to do everything simultaneously.

## Learning two: adaptability

The second thing I learnt was to adapt quickly. The two directions we weighed
were task automation and task organization. The first one we explored was task
automation: using LLMs to handle his tasks for him, so the work could get done
without him doing it by hand. We dropped it. A lot of those tasks were sending
mails to clients, and each mail needed a good deal of prior context to get
right. He was also particular about the wording and the kind of mail that
reaches a client. That meant he would have spent longer reviewing what the model
wrote than he would have spent writing the mail himself, so in practice it would
not have saved him much time.

Letting go of that idea was the lesson. Working it out from a conversation cost
us nothing. Working it out after building the product would have cost weeks. It
is far cheaper to change direction at the solutioning stage than after the idea
is written in code, and being willing to do that is what keeps a project moving.
That pushed us toward the second direction, task organization, which is where the
dashboard came from.

## What we settled on

We landed on a dashboard instead. The first thing it had to do was simple: show
everything in one clean, organized place. He took to it straight away, and it
solved one of his biggest asks, which was getting rid of the lead spreadsheet
for good.

In concrete terms, the dashboard had to:

- Hold hundreds of clients and leads in one place instead of scattered rows.
- Show a clear status for each client and lead and how each one is progressing.
- Track the deadlines on the deliverables owed to each customer.
- Keep a record of the interactions with each client and lead over time.
- Allow him to interact with the dashboard via voice

The old spreadsheet was the thing I migrated away from. The dashboard was built
to replace it as the single source of truth, not sit next to it as one more
place to keep updated.

## Why this phase matters

A lot of implementation today can be handed to generative AI, which pushes the
hard part back up the pipeline onto the requirements. The old rule still holds:
garbage in, garbage out. If you cannot refine what you feed the model, starting
with a clear and validated spec, the output is only ever as good as the input.

That is what makes filtering and adapting worth getting right. It is easy to
treat requirement gathering as the boring bit before the real work starts. On
this project it was the real work.
