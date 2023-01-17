---
layout: post
title:  "Building Web Applications in Pure Go"
date:   2023-01-17
categories: golang wasm gio gio-ui
---

For my next project, I wanted to build a wasm application, but this time hosted on a static website provider (e.g. Github Pages). From searching online there are two broad projects which can accomplish this:

* [Gio](https://gioui.org/)
* [Go-app](https://go-app.dev/)

Both of which support static websites as a way of hosting applications via WASM. One of the weird side-effects of using a under-powered computer was simply that I couldn't even run Go-app as I ran out of memory!

This means that I will probably explore Gio for future applications as a way to distribute a Go application online.

Although I haven't done much in Gio (besides running through a tutorial), I want to think about how we can easily translate things from a CLI application to a simple application. I don't intend on building "beautiful" applications, simply functional ones. Perhaps as part of this journey is looking at how we can build functional ones. 

## What application will you be building?

I've been interested in my own game master emulators - not to compete with existing ones, but simply as a learning experience using setups which fall under Creative Commons (n.b. this is partially inspired due to the current drama around OGL license, but that is a discussion for another day). What the layout of the simple application should look like or inspired by [The GameMaster's Apprentice](https://www.kickstarter.com/projects/larcenous-designs/the-gamemasters-apprentice).

## Rough Layout

We'll have different "cards" which provide different pieces of information, probably as a first cut it will only purely be text. 

```
Likely Odds
- Bad:   Yes
- Even:  No
- Good:  Yes
```

```
Dice Roll
- D6:    4
- D12:   9
- D20:   13
```

```
Plot Hook
- Objective:     Restore something broken
- Rewards:       Money or valuables
- Focus:         Enemies
```

```
Location
- Type:          Transitional area
- Identity:      None
- Goal:          A useful tool, key or device
```

```
Character
- Type:          A powerful organization
- Identity:      Entertainer
- Goal:          Obtain
- Features:      Quirk or mannerism
- Detail:        Unsavory
- Focus:         Equipment
```

There could be more categories, and stylised in different ways. Hopefully this is a simple, fun little project to get familiar with gio-ui and open up opportunities in the future to share mini-applications to the world.


