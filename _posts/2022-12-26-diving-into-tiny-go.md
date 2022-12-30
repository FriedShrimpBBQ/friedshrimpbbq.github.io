---
layout: post
title:  "Diving into...TinyGo?"
date:   2022-12-26
categories: golang tinygo
---

I'm fascinated with `WebAssembly`, and everything it promises, imagine that - you compile a program once and it can run absolutely anywhere via any [compatible](https://wasmer.io/) [runtime](https://wasmtime.dev).

Although its not mature on all platforms and programming languages, it does have strong support in Rust and Typescript/AssemblyScript, and is the technology which underpins [Cosmos](https://github.com/CosmWasm/cosmwasm).

Let's take a look at [TinyGo](https://tinygo.org/), an implementation of Go to run on the edge that compiles down the `wasm`.  We'll start with the `hello world!` program and directly compile to `wasm`:
```go
package main

import "fmt"

func main() {
	fmt.Println("Hello World!")
}
```
Then to build and run:
```sh
tinygo build -o main.wasm -target=wasi -wasm-abi=generic main.go
wasmer main.wasm
```

That's it! We've managed to compile down to `wasm` and it can now run on a variety of platforms and places, including for use in Polygot projects.

`tinygo` isn't perfect though, and many libraries aren't supported, including no support for `http` as it is not yet part of the `wasm` spec. 

I think `tinygo` and `wasm` formats would be a massive boon to `tinyml`, where we want to provide a small simple model and expose it to the outside world