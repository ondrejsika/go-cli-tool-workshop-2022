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
