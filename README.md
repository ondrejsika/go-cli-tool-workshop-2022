# GO CLI Tool Workshop 2022

## Create repo & essentials

```
mkdir hello
cd hello
```

Create Git repo

```
git init
```

Create `README.md`

```markdown
# hello cli
```

Commit changes

```
git add .
git commit -m "init: Initial commit"
```

Create `.gitignore`:

```
# Mac
.DS_Store

# Editor
.vscode
.idea

# Generic
*.log
*.backup

# App
/dist/**
/hello
/hello.exe

```

Create `.editorconfig`:

```
root = true
[*]
indent_style = space
indent_size = 2
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true
end_of_line = lf
max_line_length = off
[Makefile]
indent_style = tab
[*.go]
indent_style = tab

```

Commit changes

```
git add .
git commit -m "chore: Add editorconfig & gitignore"
```

## Init Go Project

```
go mod init hello
```

Commit changes

```
git add .
git commit -m "chore(go): go mod init"
```

## Create Hello World

Create `main.go`

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello World!")
}

```

Run

```
go run main.go
```

Build & run:

```
go build
./hello
```

Commit changes

```
git add .
git commit -m "feat: Create Hello World functionality"
```

## Install Cobra

Install Cobra

```
go get -u github.com/spf13/cobra@latest
```

Commit changes

```
git add .
git commit -m "chore(go): add cobra"
```

## Rewrite with Cobra

mkdirs

```
mkdir cmd
mkdir cmd/root
mkdir version
```

Create `version/version.go`

```go
package version

var Version string = "v0.1.0-dev"
```

Create `cmd/root/root.go`

```go
package root

import (
	"fmt"
	"hello/version"

	"github.com/spf13/cobra"
)

var Cmd = &cobra.Command{
	Use:   "hello",
	Short: "hello, " + version.Version,
	Args:  cobra.NoArgs,
	Run: func(c *cobra.Command, args []string) {
		fmt.Println("Hello World!")
	},
}
```

Create `cmd/cmd.go`

```go
package cmd

import (
	"hello/cmd/root"

	"github.com/spf13/cobra"
)

func Execute() {
	cobra.CheckErr(root.Cmd.Execute())
}
```

Update `main.go`

```go
package main

import (
	"hello/cmd"
)

func main() {
	cmd.Execute()
}
```

Clean `mod.go`:

```
go mod tidy
```

Commit changes

```
git add .
git commit -m "refactor: Rewrite using Cobra"
```

## Create version command

mkdir

```
mkdir cmd/version
```

Create `cmd/version/version.go`

```go
package version

import (
	"fmt"

	"hello/cmd/root"
	"hello/version"

	"github.com/spf13/cobra"
)

var Cmd = &cobra.Command{
	Use:     "version",
	Short:   "Prints version",
	Aliases: []string{"v"},
	Args:    cobra.NoArgs,
	Run: func(c *cobra.Command, args []string) {
		fmt.Println(version.Version)
	},
}

func init() {
	root.Cmd.AddCommand(Cmd)
}
```

Import version module in `cmd/cmd.go`:

Add `_ "hello/cmd/version"` to imports

```go
package cmd

import (
	"hello/cmd/root"
	_ "hello/cmd/version"

	"github.com/spf13/cobra"
)

func Execute() {
	cobra.CheckErr(root.Cmd.Execute())
}
```

Commit changes:

```
git add .
git commit -m "feat(cmd/version): Add version command"
```

## Add parameters

Update `cmd/root/root.go`:

```go
package root

import (
	"fmt"
	"hello/version"

	"github.com/spf13/cobra"
)

var FlagName string

var Cmd = &cobra.Command{
	Use:   "hello",
	Short: "hello, " + version.Version,
	Args:  cobra.NoArgs,
	Run: func(c *cobra.Command, args []string) {
		fmt.Printf("Hello %s!\n", FlagName)
	},
}

func init() {
	Cmd.Flags().StringVarP(
		&FlagName,
		"name",
		"n",
		"World",
		"Name to say hello",
	)
}
```

Commit changes:

```
git add .
git commit -m "feat(cmd/root): Add --name parameter"
```

## Add Goreleaser

Create `.goreleaser.yml`

```yaml
project_name: hello

before:
  hooks:
    - rm -rf ./dist
    - go mod tidy
    - git push
    - git push --tags
builds:
  -
    env:
      - CGO_ENABLED=0
    mod_timestamp: "{{ .CommitTimestamp }}"
    flags:
      - -trimpath
    ldflags:
      - -s
      - -w
      - -X hello/version.Version=v{{.Version}}
    goos:
      - windows
      - linux
      - darwin
    goarch:
      - amd64
      - "386"
      - arm
      - arm64
    goarm:
      - "6"
      - "7"
    ignore:
      - goos: darwin
        goarch: "386"
      - goos: windows
        goarch: "arm"
      - goos: windows
        goarch: "arm64"
      - goos: linux
        goarch: arm
        goarm: "6"
    binary: "{{ .ProjectName }}"

archives:
  - format: tar.gz
    name_template: "{{ .ProjectName }}_v{{ .Version }}_{{ .Os }}_{{ .Arch }}"

release:
  prerelease: auto

checksum:
  name_template: "{{ .ProjectName }}_checksums.txt"
  algorithm: sha256

brews:
  -
    name: "{{ .ProjectName }}"
    tap:
      owner: go-cli-tool-workshop-2022
      name: homebrew-tap
    homepage: https://github.com/go-cli-tool-workshop-2022/hello
    url_template: "https://github.com/go-cli-tool-workshop-2022/hello/releases/download/{{ .Tag }}/{{ .ArtifactName }}"
    folder: Formula
    caveats: "How to use this binary: https://github.com/go-cli-tool-workshop-2022/hello"
    description: "Hello World CLI"
    install: |
      bin.install "{{ .ProjectName }}"
    test: |
      system "#{bin}/{{ .ProjectName }} version"

scoop:
  bucket:
    owner: go-cli-tool-workshop-2022
    name: scoop-bucket
  homepage:  https://github.com/go-cli-tool-workshop-2022/hello
  description: Hello World CLI
  license: MIT

```

Commit changes:

```
git add .
git commit -m "chore(goreleaser): Add .goreleaser.yml"
```

## Create Organization & Repositories on Github

- Create organization `go-cli-tool-workshop-2022`
- Create repositories:
  - `go-cli-tool-workshop-2022/hello`
  - `go-cli-tool-workshop-2022/homebrew-tap`
  - `go-cli-tool-workshop-2022/scoop-bucket`


## Push to Github

```
git remote add origin git@github.com:go-cli-tool-workshop-2022/hello.git
git push -u origin master
```

## Create Release

Update `version/version.go`:

```
package version

var Version string = "v0.1.0"
```

Commit changes:

```
git add .
git commit -m "VERSION: v0.1.0"
```

Tag release:

```
git tag v0.1.0
```

Export Github token

```
export GITHUB_TOKEN=...
```

Build & release using Goreleaser

```
goreleaser
```

Update `version/version.go` after release:

```
package version

var Version string = "v0.2.0-dev"
```

Commit changes:

```
git add .
git commit -m "VERSION: v0.2.0-dev"
```

## Install

Mac

```
brew install go-cli-tool-workshop-2022/tap/hello
```

Linux

```
VERSION=v0.1.0 && \
OS=linux && \
ARCH=amd64 && \
ORG=go-cli-tool-workshop-2022 && \
BIN=hello && \
curl -fsSL https://github.com/${ORG}/${BIN}/releases/download/${VERSION}/${BIN}_${VERSION}_${OS}_${ARCH}.tar.gz -o ${BIN}_${VERSION}_${OS}_${ARCH}.tar.gz && \
tar -xvzf ${BIN}_${VERSION}_${OS}_${ARCH}.tar.gz ${BIN} && \
rm ${BIN}_${VERSION}_${OS}_${ARCH}.tar.gz && \
mv ${BIN} /usr/local/bin/
```
