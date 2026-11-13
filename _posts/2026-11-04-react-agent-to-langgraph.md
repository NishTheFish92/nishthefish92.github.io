---
title: "Moving my Spotify agent from a ReAct agent to LangGraph"
date: 2026-11-04 12:00:00 +0000
tags: [langchain, langgraph, agents, evals, spotify]
---

This post is about why I moved my [Spotify Helper Agent](https://github.com/NishTheFish92/Spotify_Helper_Agent)
off LangChain's ReAct agent and onto LangGraph, what the migration gave me, and
how I checked that it actually works.

## Where it started

I started with a simple idea which was to type what you want in plain English and have it turned into
Spotify actions, like creating a playlist, adding songs, or making it private. A
simple agent felt like the right fit, so I started with LangChain's ReAct agent
through `initialize_agent`. It takes an input, reasons about which tool to use,
acts, and repeats. It is easy to get running, but it is an abstraction over that
whole loop, so you don't get much control over the flow itself.

Fun fact: that first version had all its tools working by late October 2025,
a few weeks before Spotify launched Premium Platinum in November 2025, a plan
that bundles AI playlist creation into the app.

## What wasn't working

Every tool took a single string. The LLM writes the tool input as text, and the
tool has to parse it, so I hand-wrote that parsing into every tool:

```python
def add_songs_to_playlist(input_str: str):
    songs = ast.literal_eval(input_str)   # "['id1', 'id2', 'Gym']"
    playlist_name = songs.pop()
    ...
```

That works as long as the LLM sticks to the format. When it doesn't, the parse
fails inside the tool and the whole request fails with it.

The agent was also stateless. Every request was handled on its own, so if I
created a playlist and wanted to delete it right after, I had to spell out the
name all over again. There was no conversation, just input, logic, output.

On top of that, LangChain kept hollering about deprecation warnings on every run,
recommending LangGraph. I anyways needed something more robust than what I already had, so why not?

## What the migration changed

In LangGraph you build the agent from ground up. The state, the nodes, and
the edges between them, including conditional edges that decide where to go
next. Building it this way taught me a lot more about how an agentic workflow is
put together than the prebuilt agent did.

The core is still a reason-and-act loop: `call_model` decides what to do and
`tools` runs it. The difference is that I own the loop now, so I can add steps:

```python
graph.add_edge(START, "call_model")
graph.add_conditional_edges("call_model", tool_decision,
                            {"tools": "tools", "approve": "approve", END: END})
graph.add_edge("tools", "call_model")
```

The `approve` branch is the human-in-the-loop part. If the model tries to delete
a playlist or remove songs, the graph pauses with `interrupt` and asks me to
confirm first. If I say no, the model is told the action was cancelled.

A checkpointer (`MemorySaver`) saves the conversation state between turns, so I
can create a playlist and then just say "delete it". For now it is in-memory
only, so the context lasts for one session.

I also rewrote the tools with typed arguments using LangChain's `@tool`
decorator. The model now calls them through function calling against a schema,
and the tool receives real types instead of a string to parse:

```python
@tool(parse_docstring=True)
def add_songs_to_playlist(song_ids: list[str], playlist_name: str):
    ...
```

Hooray! No more fragile string parsing.

## Checking that it works

Since I was already making the pipeline more robust, I added evals. The simplest
check is using it myself and adding songs to my own Spotify (which I did do a lot because I found it cool how I could control my entire Spotify through text), but I also wanted
something repeatable. So, I went with a golden set of prompts, each with pre determined expected behavior.

I drafted the 22 prompts with Claude's help, with a few requirements. Some had
to be straightforward, some ambiguous or missing information, and some had to be
traps. Traps are prompts that sound like a deletion but aren't, like "I'm thinking of
cleaning up my Old Mix playlist, what do you think?" Deleting there would be a
destructive action the user never asked for.

Each prompt goes to the model once and I grade which tool it picks and with what
arguments, without running the tools. The grading is fixed, but the LLM isn't,
so every prompt runs three times, for 66 runs in total.

63 of the 66 runs passed (95%). The model never picked a delete tool unless the
prompt actually asked for one, traps included. The three failures were all on
incomplete prompts like "Make it private", where it assumed a playlist name
instead of asking which one.

## Looking back

The ReAct agent was a good place to start and got the idea working quickly. But
once I wanted the agent to remember things and check with me before doing
anything destructive, I needed control over the flow itself. The migration gave
me that, and the evals showed me where the agent still guesses when it should
ask.
