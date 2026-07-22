# The Bomb Project — A Brief Introduction

## Learning Objectives

After this lesson you should be able to:
- Describe the Bomb Project workflow and the scoring/explosion rules.
- Use **`strings`** to find clues embedded in the executable.
- Use **`gdb`** to set breakpoints, single-step, view disassembly, and inspect registers/memory.
- Apply a reverse-engineering strategy: watch **argument registers** and **return values**, and locate the **explode decision**.

> **The core skill being tested:** reading X86-64 (Unit 10–11) well enough to infer what input each phase compares against. Everything you learned about registers, the stack, and arrays/structs is now applied to a real binary.

## Overview

You are given an **executable program (the "bomb")** and must figure out the **right inputs to "defuse" it**.

- The bomb has **6 phases**, each expecting a specific input.
- You provide input as **text** — *a text file is recommended* (so you don't retype after each run).
- Scoring is **automatic**, with an **online scoreboard**:
  - **−½ point per explosion**, up to a **maximum of 20 points off**.

This project is where everything comes together: you **reverse-engineer** X86-64 assembly to discover what each phase wants.

## Getting Started

1. **Read the handout.**
2. **Download your bomb** — you must be on the **department network or VPN**. **Each student has their own unique bomb.**
3. You may work with others in **small groups**, but **you must defuse your own bomb**. Don't just hand over answers — **guide each other's learning.**
4. **Read the gdb-primer.**
5. **Do the lab**, then **defuse your bomb.**

## The Core Tools

### `strings`
Prints the printable character sequences embedded in the executable — a quick way to spot expected inputs or hints.

### `gdb` (GNU Debugger)
A **wrapper around the program** that lets you observe it as it runs:
- **Set breakpoints**
- **Single-step** through instructions
- **View the assembly**
- **View the processor state** (registers, memory)
- **Stop the program mid-execution** (e.g., right before it would explode)

## Strategy Tips

- **Look at the arguments** going into functions (`%rdi`, `%rsi`, … — recall the argument-register order).
- **Look at return values** from function calls (in `%rax`).
- **Find where it decides to explode**, then figure out how to make that branch **not** taken.
- **Don't dive into system calls** (`scanf`, `printf`, etc.) — those are library code, not the puzzle.
- **Don't go down rabbit holes** — stay focused on what controls the explode/defuse decision.
- **Bounce ideas off someone else.**
- **Have fun!**

## Common Pitfalls & Misconceptions

- **Detonating while exploring.** Set a **breakpoint on the explode function** *before* you run, so a wrong guess stops instead of costing points. Run under `gdb`, not standalone.
- **Stepping into library calls.** `stepi` into `scanf`/`printf`/`strcmp` drops you into pages of library code. Use a **breakpoint after** the call and read the result in `%rax` instead.
- **Reading registers at the wrong moment.** Argument registers (`%rdi`, `%rsi`, …) hold the arguments **at the moment of the call**; later instructions overwrite them. Inspect them right at the comparison/call you care about.
- **Editing input interactively every run.** Keep your answers in a **text file** and feed it in, so re-running is instant and reproducible.
- **Sharing answers instead of methods.** Each bomb is unique, so a copied answer won't even work — and the point is to learn the technique. Help each other with *how*, not *what*.

## Key Takeaways

- The bomb is a **reverse-engineering** exercise: read the assembly, deduce the required input for each of 6 phases.
- `strings` for quick clues; **`gdb`** for breakpoints, single-stepping, and inspecting registers/memory.
- Focus on **argument registers, return values, and the explode decision**; skip library internals and rabbit holes.
- Each explosion costs ½ point (max 20 off) — so set breakpoints to **stop before it explodes** while you experiment.


```masteryls
{"id":"4e87e3c6-167c-4b1f-bf1c-42e5fe9412d6", "title":"Essay", "type":"essay", "gradingCriteria":"- Addresses the prompt directly\n- Uses at least one concrete example\n- Demonstrates accurate understanding of key concepts" }
Why is providing input from a text file better than typing it each run?
```
```masteryls
{"id":"b32a914e-532c-4a21-be4b-1a44a0c0962f","title":"Essay","type":"essay","gradingCriteria":"- Addresses the prompt directly\n- Uses at least one concrete example\n- Demonstrates accurate understanding of key concepts"}
Which register holds a function's return value, and why is that useful when defusing a phase?
```
```masteryls
{"id":"2dd4db29-3a25-434e-b047-c81c66ec0f63","title":"Essay","type":"essay","gradingCriteria":"- Addresses the prompt directly\n- Uses at least one concrete example\n- Demonstrates accurate understanding of key concepts"}
What does setting a breakpoint *before* the explode function let you do?
```
```masteryls
{"id":"b8c07b68-8c21-4e68-98e7-32e8dd560c3a","title":"Essay","type":"essay","gradingCriteria":"- Addresses the prompt directly\n- Uses at least one concrete example\n- Demonstrates accurate understanding of key concepts"}
Why are you advised **not** to step into `scanf`/`printf`?
```
