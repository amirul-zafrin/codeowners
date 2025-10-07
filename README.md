# codeowners

![build](https://github.com/hmarr/codeowners/workflows/build/badge.svg)
[![PkgGoDev](https://pkg.go.dev/badge/github.com/hmarr/codeowners)](https://pkg.go.dev/github.com/hmarr/codeowners)

A CLI and Go library for GitHub's [CODEOWNERS file](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/about-code-owners#codeowners-syntax).

## Installation

Install the CLI from the [homebrew tap](https://github.com/hmarr/homebrew-tap#codeowners).

```console
$ brew tap hmarr/tap
$ brew install codeowners
```

Install the library with `go get`.

```console
$ go get github.com/hmarr/codeowners@latest
```

## Command line usage

By default, the command line tool will walk the directory tree, printing the code owners of any files that are found.

```console
$ codeowners --help
usage: codeowners <path>...
  -f, --file string     CODEOWNERS file path
  -h, --help            show this help message
  -o, --owner strings   filter results by owner

$ ls
CODEOWNERS       DOCUMENTATION.md README.md        example.go       example_test.go

$ cat CODEOWNERS
*.go       @example/go-engineers
*.md       @example/docs-writers
README.md  product-manager@example.com

$ codeowners
CODEOWNERS                           (unowned)
README.md                            product-manager@example.com
example_test.go                      @example/go-engineers
example.go                           @example/go-engineers
DOCUMENTATION.md                     @example/docs-writers
```

To limit the files the tool looks at, provide one or more paths as arguments.

```console
$ codeowners *.md
README.md                            product-manager@example.com
DOCUMENTATION.md                     @example/docs-writers
```

Pass the `--owner` flag to filter results by a specific owner.

```console
$ codeowners -o @example/go-engineers
example_test.go                      @example/go-engineers
example.go                           @example/go-engineers
```

## Go library usage

Documentation is available at [pkg.go.dev](https://pkg.go.dev/github.com/hmarr/codeowners).

```go
package main

import (
	"fmt"
	"log"
	"os"

	"github.com/amirul-zafrin/codeowners"
)

func main() {
	file, err := os.Open("CODEOWNERS")
	if err != nil {
		log.Fatal(err)
	}

	ruleset, err := codeowners.ParseFile(file)
	if err != nil {
		log.Fatal(err)
	}

	rule, err := ruleset.Match("path/to/file")
	if err != nil {
		log.Fatal(err)
	}

	fmt.Printf("Owners: %v\n", rule.Owners)
}
```
