---
layout: post
title:  "Starship.rs is Pretty Cool"
date:   2023-02-24
categories: bash shell starship
---

In the land of consoles and shell tools, I came across [Starship](https://starship.rs/). Of course it isn't the only shell tool, there is the extremely popular [oh-my-zsh](https://ohmyz.sh/) as well. One of the reasons to use Starship is because some places lock down software, but allow `brew` - there are many reasons for this, for example avoiding software that "phones home" by default (oh-my-zsh checks for updates). Although it is trivial to switch these kinds of functionality off, most IT policies go for the more conservative approach - that is to outright ban software they don't want to support without a "sanitized" package manager. 

That being said, Starship is not a drop-in replacement for oh-my-zsh. One would argue instead they compliment each other. Although many things that Starship does can be done via oh-my-zsh plugins, in general the defaults ... speak for themselves!

For example:
- It can pick up the version of the Python virtualenv you're in (and more R, Rust, node etc...)
- It understands what AWS profile and region is your current context
- You can _set timeouts for commands_: this feature surprised me so much, I don't think I can imagine NOT having it on!


