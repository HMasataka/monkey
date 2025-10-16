# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Monkey is an interpreted programming language implementation written in Go. It follows a classic compiler architecture with distinct phases for lexing, parsing, AST representation, and evaluation.

## Build & Run Commands

```bash
# Build the executable
go build -o monkey

# Run the REPL (interactive interpreter)
./monkey

# Or run directly
go run main.go
```

## Testing Commands

```bash
# Run all tests
go test ./...

# Run tests with verbose output
go test -v ./...

# Run tests for a specific package
go test -v ./evaluator
go test -v ./parser
go test -v ./lexer

# Run a specific test function
go test -v ./evaluator -run TestEvalIntegerExpression

# Run tests with coverage
go test -cover ./...
```

## Code Architecture

The codebase follows a traditional interpreter pipeline:

1. **Token** (`token/`): Defines all token types (keywords, operators, delimiters, literals) used by the lexer.

2. **Lexer** (`lexer/lexer.go`): Converts raw source code string into tokens. The lexer reads characters sequentially, identifying and classifying tokens.

3. **Parser** (`parser/parser.go`): Consumes tokens from the lexer and builds an Abstract Syntax Tree (AST). Uses Pratt parsing for operator precedence handling via `prefixParseFns` and `infixParseFns` maps.

4. **AST** (`ast/ast.go`): Defines the node types representing program structure. Key interfaces: `Node`, `Statement`, and `Expression`. The `Program` node is the root containing all statements.

5. **Object System** (`object/`): Runtime object representation. All runtime values implement the `Object` interface with `Type()` and `Inspect()` methods. Supported types include Integer, Boolean, String, Array, Hash, Function, Builtin, and Error.
   - **Environment** (`object/enviroment.go`): Maps identifiers to runtime objects. Supports nested scopes with outer scope references for closures.

6. **Evaluator** (`evaluator/evaluator.go`): Tree-walking interpreter that recursively evaluates AST nodes. The `Eval` function is the main entry point. Returns runtime `Object` values.
   - **Builtins** (`evaluator/builtin.go`): Implements built-in functions like `len()`, `puts()`, etc.

7. **REPL** (`repl/repl.go`): Interactive read-eval-print loop for the interpreter.

## Known Issues

When modifying the evaluator API (e.g., changing `Eval` signature), ensure REPL is updated accordingly. The repl uses `evaluator.Eval` and must receive the correct parameters (node and environment).

## Development Notes

- Type system is checked at runtime during evaluation (dynamic typing).
- Error handling uses `object.Error` objects that propagate through evaluation.
- Functions are first-class objects supporting closures via environment captures.
- Hash and array literals support complex nested structures.
