---
title: "Dockerizing Go Modules"
date: 2019-11-17T15:08:04-08:00
draft: true
---
This will be an intro to Dockerizeing go modules and multi-stage Docker builds.

Let's start with a simple Hello World webserver:
```go
package main

import (
	"fmt"
	"log"
	"net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "Hello world!")
}

func main() {
	http.HandleFunc("/", handler)
	log.Fatal(http.ListenAndServe(":8080", nil))
}
```

Initialize Go Modules:
```
$ go mod init test-app
```
This creates a `go.mod` file which locks down version dependencies. So far, our current working directory looks like this:
```
$ ls
go.mod
main.go
```

Now let's add a file called `Dockerfile` into our current working directory:
```Dockerfile
FROM golang:1.13-alpine3.10 AS build

# cache installed dependencies first
WORKDIR /app
COPY go.mod .
RUN go mod download

# compile our app
COPY . .
RUN go build -o webapp

# use the intermediate executable to make a minimal container
FROM alpine:3.10
COPY --from=build /app/webapp .
ENTRYPOINT [ "/webapp" ]
```

To build and run our container:
```
$ docker build --tag=webapp .
$ docker run -p 8080:8080 webapp
```