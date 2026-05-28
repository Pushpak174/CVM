# CVM++ — Custom Virtual Machine in C++

> A minimal, end-to-end programming language pipeline built in C++ — from raw source text to bytecode execution.

[![Language](https://img.shields.io/badge/language-C%2B%2B17-blue.svg)](https://isocpp.org/)
[![Build](https://img.shields.io/badge/build-CMake%203.10%2B-brightgreen.svg)](https://cmake.org/)
[![Status](https://img.shields.io/badge/status-active-success.svg)]()

---

## Overview

CVM++ is a ground-up implementation of a small programming language runtime, designed to demonstrate every stage of the compilation pipeline — from lexical analysis to virtual machine execution.

```
Source Code  →  Tokens  →  AST  →  Bytecode  →  VM Execution
```

This project was built as an exercise in understanding how real-world languages (Python, Java, Lua) work under the hood — no libraries, no shortcuts.

---

## Features

- **Custom language** with variable declarations, arithmetic, and print statements
- **Full pipeline implementation:** Lexer → Parser → AST → Compiler → VM
- **Stack-based bytecode VM** with a flat variable store
- **REPL mode** for interactive evaluation
- **File execution** mode for running `.cvm` scripts

---

## Project Structure

```
CVM_PlusPlus/
├── CMakeLists.txt
├── src/
│   └── main.cpp           # Entry point: REPL and file runner
├── include/
│   ├── Common.h           # Shared types and opcode definitions
│   ├── Lexer.h            # Tokenizer
│   ├── Parser.h           # AST construction
│   ├── Compiler.h         # AST → Bytecode code generation
│   └── VM.h               # Bytecode interpreter
└── tests/
    ├── script.cvm
    ├── calculator.cvm
    └── truth_machine.cvm
```

---

## Build Instructions

**Prerequisites:** CMake ≥ 3.10, a C++17-compatible compiler (GCC, Clang, or MSVC)

```bash
mkdir build && cd build
cmake ..
cmake --build .
```

The build produces a single executable: `cvm`

---

## Usage

### Run a script file

```bash
./cvm ../tests/script.cvm
```

### Interactive REPL

```bash
./cvm
cvm> let x = 5;
cvm> print x + 10;
15
```

---

## Language Reference

CVM++ supports a minimal, C-like syntax.

### Variable Declaration

```
let x = 10;
```

### Arithmetic Expressions

```
x + y + 5
```

### Output

```
print x + y;
```

### Example Program

```c
let x = 10;
let y = 20;
print x + y;        // 30

let z = x + y + 5;
print z;            // 35
```

**Output:**
```
--- VM Execution Output ---
30
35
```

---

## Architecture Deep Dive

### 1. Lexer (`Lexer.h`)

Converts raw source text into a flat sequence of tokens. Handles keywords, identifiers, integer literals, operators, and punctuation.

```
Input:   let x = 10;
Tokens:  TOK_LET  TOK_IDENT("x")  TOK_ASSIGN  TOK_INT(10)  TOK_SEMI
```

### 2. Parser (`Parser.h`)

Consumes the token stream and produces an Abstract Syntax Tree (AST). Expressions are parsed using a recursive descent strategy.

```
print x + y + 5
         ↓
    PrintStatement
         └── BinaryExpr(+)
               ├── BinaryExpr(+)
               │     ├── Identifier("x")
               │     └── Identifier("y")
               └── IntLiteral(5)
```

### 3. Compiler (`Compiler.h`)

Performs a depth-first traversal of the AST and emits stack-based bytecode instructions.

```
AST Input: let z = x + y + 5;

Bytecode Output:
  LOAD  x
  LOAD  y
  ADD
  PUSH  5
  ADD
  STORE z
```

### 4. Virtual Machine (`VM.h`)

Executes the bytecode instruction stream. Uses:
- An **operand stack** for expression evaluation
- A **variable array** (max 256 slots) for named bindings

---

## Instruction Set

| Opcode     | Operand  | Description                              |
|------------|----------|------------------------------------------|
| `OP_PUSH`  | `value`  | Push an integer constant onto the stack  |
| `OP_ADD`   | —        | Pop two values, push their sum           |
| `OP_STORE` | `index`  | Pop top of stack, store in variable slot |
| `OP_LOAD`  | `index`  | Push value of variable slot onto stack   |
| `OP_PRINT` | —        | Pop and print top of stack               |
| `OP_HALT`  | —        | Halt execution                           |

---

## Current Limitations

This is v1 — intentionally constrained to keep the implementation readable and focused.

- Integer types only (no floats or strings)
- Addition operator only
- No expression grouping (parentheses)
- No control flow (conditionals, loops)
- No functions or scope
- No runtime error messages

---

## Roadmap

- [ ] Subtraction, multiplication, division
- [ ] Operator precedence and parenthesised expressions
- [ ] Meaningful compile-time and runtime error messages
- [ ] String type support
- [ ] `if` / `else` conditionals
- [ ] `while` loops
- [ ] User-defined functions with local scope

---

## Why I Built This

Most developers use compilers and interpreters every day without understanding what happens between writing code and running it. CVM++ was an attempt to close that gap — every stage of the pipeline, written from scratch, with no external dependencies.

The goal wasn't to build something production-ready. It was to understand, at a mechanical level, how a language actually works.

---

