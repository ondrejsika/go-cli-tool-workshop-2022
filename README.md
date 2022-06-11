# go-cli-tool-workshop-2022

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
