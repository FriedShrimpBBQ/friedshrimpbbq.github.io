---
layout: post
title:  "Game Master Emulator - in Mithril.js"
date:   2023-01-21
categories: mithril mithril.js bulma openmoji
---

In my quest to write a game master emulator, I have unfortunatley strugged with GUI interfaces and deferred to what I'm comfortable with. To ensure I made progress, I quickly prototyped (and almost solved) the problem using Mithril.js, and themed with Openmoji and Bulma. The results is actually okay, though the table generation isn't quite correct.

[See here for the hosted website and play around with it!](https://friedshrimpbbq.github.io/Yet-another-Game-Master-Emulator---Mithril-Edition/)

Since this is done, is there still a need to do it in Golang? I think so! Partly to learn Go, as I was struggling a bit to figure out the correct data structures specific to Go, and also to understand how we can distribute applications in a consistent manner to both CLI and Web experiences would be an amazing thought experiment. 

I think Go with WASM support for the web is an extremely useful gateway to bridge CLI tools with Web interfaces. 

## On Openmoji

Openmoji, despite its whimsical and sometimes inconsistent theming, was amazing to use, compared with fontawesome. Fontawesome looks great when you need polish, but the color and rather _loose_ nature of Openmoji is perfect for this project. Infact for this project, I ended up using `openmoji-awesome` which replicates the interfaces provided by fontawesome, which helped with transitioning. 

Despite saying I'll use a GUI framework for Go, I might stick with using Go WASM with Mithril and Bulma in future projects. Using Go as a "backend" and Mithril as front-end separation gives me a lot of freedom and more importantly speed to work through ideas and deploy quickly. 

## On Mithril

I think Mithril plays in very much the same space as Vue - something that can modify the DOM dynamically purely on the client-side. For whatever reason, I've been more drawn to Mithril, since its composability seems more _natural_ to me at least. This is probably partially because running things like npm is horrible on my laptop and internet connection (I generally try to work offline), and it just means I have to revert to _simplier_ setups. In the future, I may look into other workflows in the web development space, as it is essential to Web3, however at this stage, I'm going to stick with "what works for me". 

Another big reason for Mithril is not having to deal with HTML templates at all. For a solo developer that is an amazing experience as you often don't need the complexity, however I completely understand when working in a team environment why that separation may be worthwhile and useful depending on various team members roles and responsibilities. 

## On Bulma

I didn't have a strong reason to use Bulma, one reason was it seemed fairly complete as a CSS framework and was not bootstrap. Perhaps one day I'll explore Tailwind or Materialize. At this point in time, this fits in the "good enough" category for my needs.
