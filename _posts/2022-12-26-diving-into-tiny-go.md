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

I think `tinygo` and `wasm` formats would be a massive boon to `tinyml`, where we want to provide a small simple model and expose it to the outside world.

### Parsing Arguments...stdlib style
Of course just compiling a program to `wasm` isn't particularly interesting, we need to get data in! Let's have a look at how to do this via the standard library. There are two main modules: `os`, `flag`.

Let's say we wanted to read in a stream of numbers and add them up. It could look something like this:
```go
package main

import (
	"log"
	"os"
	"strconv"
)

var runningSum float64

func main() {
	for i, arg := range os.Args[1:] {
		if n, err := strconv.ParseFloat(arg, 64); err == nil {
			runningSum += n
		} else {
			log.Fatal("Unable to parse: ", i, " - ", arg)
		}
	}
	log.Println("Running sum: ", runningSum)
}
```
We read in all the elements via `os.Args[1:]` (the 0th element is the name of the program itself!), and try parsing them one at a time. Of course we can raise a warning instead with the wrong input. We'll leave that as an exercise for the reader. 
```sh
go run main.go 1 2 3 4 5
go run main.go 1 2 3 error 4 5  # errors out
```
It is also straightforward to add flags to our program. The syntax is `var ??? = flag.<Type>(<flag name>, <default value>, <description>)`. To extend the example above, let's add a greeting using `var greet = flag.String("greet", "", "Person to greet")`
```go
package main

import (
	"flag"
	"log"
	"strconv"
)

var runningSum float64

func main() {
	var greet = flag.String("greet", "", "Person to greet")
	flag.Parse()
	tail := flag.Args()
	for i, arg := range tail {
		if n, err := strconv.ParseFloat(arg, 64); err == nil {
			runningSum += n
		} else {
			log.Fatal("Unable to parse: ", i, " - ", arg)
		}
	}
	log.Println("\nHello", *greet, "!\nRunning sum: ", runningSum)
}
```
Usage:
```sh
go run main.go 1 2 3 4 5  # this still works
go run -greet shrimp 1 2 3 4 5
```
### Other Notes
To install `tinygo` it is fairly straightforward on Windows and macOS

```
# windows
scoop install go
scoop install tinygo
scoop install binaryen
scoop install wasmer

# macos
brew tap tinygo-org/tools
brew install tinygo
brew install wasmer
```

**`gofmt`**
Go comes with an opinionated formatter. Use it!
```sh
gofmt -w main.go
```


