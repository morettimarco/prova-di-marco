---
name: hello-world
description: Scaffold a minimal "Hello, World!" project in Python, TypeScript, Go, or Rust. TRIGGER when the user asks to create, generate, or scaffold a hello-world / starter / boilerplate project, or says "/hello-world". Supports an optional language and project-name argument.
---

# hello-world

Generate a minimal, runnable "Hello, World!" project.

## Inputs

The skill is invoked as `/hello-world [language] [project-name]`. Both arguments are optional.

- **language**: one of `python`, `typescript`, `ts`, `go`, `rust` (case-insensitive).
- **project-name**: directory name to create. Must be a valid identifier for the chosen language's tooling (lowercase, hyphen/underscore allowed, no spaces).

If either argument is missing, **ask the user** before generating anything. Do not guess.

## Workflow

1. **Resolve arguments.**
   - If `language` is missing or unrecognized, ask: "Which language? (python / typescript / go / rust)"
   - If `project-name` is missing, ask: "What should the project directory be called?"
   - Normalize: `ts` → `typescript`. Lowercase the language.

2. **Check the target directory.** If `./<project-name>/` already exists and is non-empty, stop and ask the user whether to overwrite. Do not clobber silently.

3. **Generate files** per the language template below using the `Write` tool. Create the project directory first via `mkdir -p`.

4. **Print the run command** for the user. Do not execute it — let them run it.

## Templates

### python

Files (project root = `<project-name>/`):

`hello.py`:
```python
def main() -> None:
    print("Hello, World!")


if __name__ == "__main__":
    main()
```

`README.md`:
```markdown
# <project-name>

Run: `python3 hello.py`
```

Run command to print: `cd <project-name> && python3 hello.py`

### typescript

Files:

`package.json`:
```json
{
  "name": "<project-name>",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "scripts": {
    "start": "tsx index.ts",
    "build": "tsc"
  },
  "devDependencies": {
    "tsx": "^4.0.0",
    "typescript": "^5.0.0"
  }
}
```

`tsconfig.json`:
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ES2022",
    "moduleResolution": "Bundler",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "outDir": "dist"
  },
  "include": ["index.ts"]
}
```

`index.ts`:
```typescript
function main(): void {
  console.log("Hello, World!");
}

main();
```

`.gitignore`:
```
node_modules/
dist/
```

Run command to print: `cd <project-name> && npm install && npm start`

### go

Files:

`go.mod` (replace `<project-name>` in the module line):
```
module <project-name>

go 1.22
```

`main.go`:
```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, World!")
}
```

Run command to print: `cd <project-name> && go run .`

### rust

Files:

`Cargo.toml`:
```toml
[package]
name = "<project-name>"
version = "0.1.0"
edition = "2021"

[dependencies]
```

`src/main.rs`:
```rust
fn main() {
    println!("Hello, World!");
}
```

`.gitignore`:
```
/target
```

Run command to print: `cd <project-name> && cargo run`

## Rules

- Generate only the files listed for the chosen language. Do not add CI configs, license files, test scaffolding, or extra dependencies.
- Substitute `<project-name>` literally everywhere it appears in the template.
- For Rust and Go, validate that `<project-name>` is a valid crate / module name (lowercase letters, digits, hyphens/underscores; cannot start with a digit). If invalid, ask for a different name.
- After writing files, respond with: a one-line confirmation, the file list, and the run command. Nothing else.
