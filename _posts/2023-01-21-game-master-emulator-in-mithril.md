---
layout: post
title:  "Game Master Emulator - in Mithril.js"
date:   2023-01-21
categories: mithril mithril.js bulma openmoji
---

In my quest to write a game master emulator, I have unfortunatley strugged with GUI interfaces and deferred to what I'm comfortable with. To ensure I made progress, I quickly prototyped (and almost solved) the problem using Mithril.js, and themed with Openmoji and Bulma. The results is actually okay, though the table generation isn't quite correct.

See [here](https://friedshrimpbbq.github.io/Yet-another-Game-Master-Emulator---Mithril-Edition/) for the hosted website and play around with it!

Since this is done, is there still a need to do it in Golang? I think so! Partly to learn Go, as I was struggling a bit to figure out the correct data structures specific to Go, and also to understand how we can distribute applications in a consistent manner to both CLI and Web experiences would be an amazing thought experiment. 

I think Go with WASM support for the web is an extremely useful gateway to bridge CLI tools with Web interfaces. 

## On Openmoji

Openmoji, despite its whimsical and sometimes inconsistent theming, was amazing to use, compared with fontawesome. Fontawesome looks great when you need polish, but the color and rather _loose_ nature of Openmoji is perfect for this project. Infact for this project, I ended up using `openmoji-awesome` which replicates the interfaces provided by fontawesome, which helped with transitioning. 

Despite saying I'll use a GUI framework for Go, I might stick with using Go WASM with Mithril and Bulma in future projects. Using Go as a "backend" and Mithril as front-end separation gives me a lot of freedom and more importantly speed to work through ideas and deploy quickly. 

