---
layout: post
title:  "Getting started with tinyjson"
date:   2023-01-03
categories: golang tinygo tinymlkit
---

One of the key things which any programmer needs is a way to read/write in structured data; whether this is for configuring things, or preserving state in an application. In [TinyGo](https://tinygo.org/), let's just say it doesn't come out of the box. 

It is also a bit unfortunate that performing a Google search doesn't provide much confidence when you see comments like this:

> I can't believe Go (TinyGo) is about to miss the boat on WASM - @gitslav Nov 11, 2022

After spending a bit too long trying to find a workable solution, I finally stumbled upon [easyjson](https://github.com/mailru/easyjson) which adds this support; though you have to use the patched version called [tinyjson](https://github.com/CosmWasm/tinyjson)

To install run:

```sh
$ go get github.com/CosmWasm/tinyjson && go install github.com/CosmWasm/tinyjson/...@latest
```

and then run:

```
$ tinyjson -all <file>.go
```

Then we should be able to get a serialisation/deserialisation working with TinyGo!

## Full Example

**Setup Dependencies**

In a new directory first initialise the go module

```sh
$ go mod init jsondemo
```

Now add the dependencies:

```
$ go get github.com/CosmWasm/tinyjson && go install github.com/CosmWasm/tinyjson/...@latest
```

Now you should be ready to go!

**Create a Schema**

Let's create a sample schema which we want to make it `json` serialisable. 

```go
// schema/person.go
package schema

type Person struct {
    Name string
	Age int
}
```

Now we can generate the serialisationg/deserialisation code:

```
$ tinyjson -all person.go
```

_note: tinyjson/easyjson doesn't support `package main`!_


Let's try using the newly generated code!

```go
// main.go
package main

import (
	"fmt"
	"jsondemo/schema"
)

func main() {
	samplePerson := &schema.Person{}
	samplePerson.UnmarshalJSON([]byte(`{"Name": "Jamie", "Age": 21}`))
	fmt.Println(samplePerson)
}
```

Finally, this should just work!

```
$ go run .
&{Jamie 21}
```

If it didn't work, check that your folder structure resembles the below:

```
.   go.mod
│   go.sum
│   main.go
│
└───schema
        person.go
        person_tinyjson.go
```

Good luck and happy coding.
