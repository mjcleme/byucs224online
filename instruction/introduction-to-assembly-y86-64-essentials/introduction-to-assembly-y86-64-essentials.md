# Introduction to Assembly: Y86-64 (Essentials)



## Learning Objectives

After this lesson you should be able to:
- Name the parts of the **programmer-visible processor state** (memory, the 15 registers, PC, condition codes, status).
- Read and write the four Y86-64 **data-movement** instructions and explain why **memory-to-memory** moves are illegal.
- Use the **ALU** instructions (`addq`, `subq`, `andq`, `xorq`) and state which set the **condition codes**.
- Translate the **condition suffixes** (`e`, `ne`, `l`, `le`, `g`, `ge`) into flag tests and use them in **jumps**.
- Explain how the simulator **fills memory** from source and what `label:`, `.pos`, `.align`, `.quad` do.

## Where We're Going

- Last time: circuits → a processor that runs assembly (bits).
- Now (two weeks): **simple assembly — Y86-64.**
- Later: **real assembly — X86-64**, connected to C code.
- Eventually: put it together to **reverse-engineer** what a program is doing from its assembly.

## What Is Assembly?

**Assembly is human-readable machine code.**

**Machine code** is the 0s and 1s that correspond to values on wires — they determine how information is routed through the CPU, the ALU, and to/from memory. Assembly gives those bit patterns readable names.

Recall: **Computer = Memory + Processing.** Assembly is how we tell the **processing** half what to do.

## The Programmer-Visible Processor State

Assembly instructions directly manipulate this state:

1. **Memory** — stores bytes for both **programs** and **data** (our familiar array of bytes).
2. **Registers** — 15 fast storage slots, each holding an **8-byte (quad-word)** value. Think of them as the CPU's **scratch paper.** All are general-purpose **except `%rsp`**, the **stack pointer**, which holds the address of the top item on the stack.

   | id | reg | id | reg | id | reg | id | reg |
   |----|-----|----|-----|----|-----|----|-----|
   | 0 | `%rax` | 4 | `%rsp` | 8 | `%r8` | C | `%r12` |
   | 1 | `%rcx` | 5 | `%rbp` | 9 | `%r9` | D | `%r13` |
   | 2 | `%rdx` | 6 | `%rsi` | A | `%r10` | E | `%r14` |
   | 3 | `%rbx` | 7 | `%rdi` | B | `%r11` | F | (none) |

3. **PC (Program Counter)** — the **address of the next instruction**.
4. **Condition Codes** — one-bit flags showing the result of the most recent math/logic op: **ZF** (zero), **SF** (sign), **OF** (overflow).
5. **Stat (Program Status):**
   - **AOK** — normal operation
   - **HLT** — halted by a `halt` instruction
   - **ADR** — invalid address encountered
   - **INS** — invalid instruction encountered

> Every instruction is **simple and basic** — it usually changes just **one thing** about the processor state.

## Why Y86-64?

- **Y86-64** is a **simpler** assembly, good for learning.
- **X86-64** is the real thing — much more complicated, with many more instructions.
- Y86-64 is essentially a **subset** of X86-64.

### A note on sizes
A leading/trailing letter indicates operand size:
- **(b)** byte = 8 bits, **(w)** word = 16 bits, **(l)** double-word = 32 bits, **(q)** quad-word = 64 bits.
- **Y86-64 only operates on quad-word (`q`) values.** (X86-64 uses the others too.)

Each instruction also has a **byte encoding** (covered in a later deck); for now, focus on the **text** form.

## The Complete Y86-64 Instruction Set ("That's all, folks!")

- `halt` — stop the processor
- `nop` — do nothing
- **Move data:** `rrmovq` (reg→reg), `irmovq` (immediate→reg), `rmmovq` (reg→memory), `mrmovq` (memory→reg), conditional moves `cmovXX`
- **ALU ops (on registers):** `addq`, `subq`, `andq`, `xorq`
- **Conditional jumps:** `jXX` (change the PC)
- **Stack:** `pushq` / `popq`
- **Functions:** `call`, `ret`

## Data Movement

The instruction name encodes the **source** and **destination** kinds:

| Instruction | Meaning | Example |
|-------------|---------|---------|
| `rrmovq rA, rB` | copy register `rA` → register `rB` | `rrmovq %rax, %rbx` |
| `irmovq V, rB` | store immediate value `V` → register `rB` | `irmovq $0x54, %rcx` |
| `rmmovq rA, D(rB)` | copy `rA` → memory at address `[D + rB]` | `rmmovq %rax, 8(%rdi)` |
| `mrmovq D(rB), rA` | copy memory at `[D + rB]` → register `rA` | `mrmovq (%rsi), %rdx` |

- Immediates must be written with a leading **`$`**.
- **Memory-to-memory movement is not allowed** — you must go through a register.

## ALU Operations

ALU ops **always operate on registers and store the result to a register.** The **destination is also a source**, so one register gets **overwritten** (you may use the same register for both sources).

| Instruction | Effect | Example |
|-------------|--------|---------|
| `addq rA, rB` | `rB = rB + rA` | `addq %rax, %rbx` |
| `subq rA, rB` | `rB = rB − rA` | `subq %r10, %rcx` |
| `andq rA, rB` | `rB = rB & rA` | `andq %rdi, %r8` |
| `xorq rA, rB` | `rB = rB ^ rA` | `xorq %rdx, %rsi` |

**These instructions set the condition codes.**

## Condition Codes (set by ALU ops)

- **ZF (Zero):** 1 if the most recent op result was exactly 0.
- **SF (Sign):** 1 if the result was negative.
- **OF (Overflow):** 1 if the op caused a two's-complement overflow.

### Condition combinations (used by conditional jumps/moves)
ALU ops set one-bit flags: **ZF** (result was 0), **SF** (result was negative), **OF** (two's-complement overflow).

> **The trick:** imagine the flags came from a **subtract** computing `A − B = V`. Each condition then describes the relationship of **A to B**.

| Suffix | Meaning | Flag test |
|--------|---------|-----------|
| `e` | equal | `ZF` |
| `e` | A == B | `V` would be 0 → check ZF | `ZF` |
| `ne` | not equal | `~ZF` |
| `ne` | A ≠ B | `V` ≠ 0 → ZF not set | `~ZF` |
| `l` | less | `SF ^ OF` |
| `l` | A < B | `V` negative; SF *or* overflow flipped the sign | `SF ^ OF` |
| `le` | less or equal | `(SF ^ OF) \| ZF` |
| `le` | A ≤ B | `l` **or** `e` | `(SF ^ OF) \| ZF` |
| `g` | greater | `~(SF ^ OF) & ~ZF` |
| `g` | A > B | not `l` **and** not `e` | `~(SF ^ OF) & ~ZF` |
| `ge` | greater or equal | `~(SF ^ OF)` |
| `ge` | A ≥ B | not `l` | `~(SF ^ OF)` |

## Conditional Moves

A conditional move copies `rA → rB` **only if** the condition is satisfied:

`cmove`, `cmovne`, `cmovl`, `cmovle`, `cmovg`, `cmovge` (e.g., `cmove %rax, %rbx`).

> `rrmovq rA, rB` is really just an **unconditional** conditional move.

Conditional moves let you implement simple `if` logic **without a branch/jump**.

## Conditional Jumps (Control Flow)

A jump sets the **PC** to `Dest` **if** the condition holds:
- `jmp Dest` — **always** jump (unconditional)
- `je`, `jne`, `jl`, `jle`, `jg`, `jge` — jump if the matching condition is met

Jumps build **loops and if/else**. In the simulator, `Dest` can be a **hardcoded address** or a **label** (`jmp 24` or `jmp loop`).

## Stack Instructions

> **The stack grows *down* in memory** (toward lower addresses), and **`%rsp` points to the top item.**

**`pushq rA`** does two things:
1. **Decrement `%rsp` by 8** (one quad-word).
2. Store the value in `rA` at the memory address now in `%rsp`.

**`popq rA`** does two things:
1. Read the value at the address in `%rsp` into `rA`.
2. **Increment `%rsp` by 8.**

## Function Instructions

Used for function calls (detailed in the next deck):

**`call Dest`:**
1. **Push the address of the next instruction** (the return address) onto the stack.
2. Set the **PC to `Dest`** (the function's first instruction).

**`ret`:**
- **Pop the return address** off the stack and set the PC to it — resuming where the function was called from.

For these to work, **`%rsp` must be pointing at the right place.**

## Worked Example: compute `0x200 + 0x24` into `%rax`

```asm
irmovq $0x200, %rax     # %rax = 0x200
irmovq $0x24,  %rcx     # %rcx = 0x24
addq   %rcx,   %rax     # %rax = %rax + %rcx = 0x224
```
Step by step the registers go: `%rax: 0 → 0x200 → 0x224`, `%rcx: 0 → 0x24`. After `addq`, the condition codes are set (here ZF=SF=OF=0).

## The Simulator and Filling Memory

Think of the assembly source as **instructions telling the simulator how to fill up memory**. It starts at address 0 and places each encoded instruction at the next address, unless a command says otherwise.

**Mental model (two passes):**
1. The simulator does a pass to figure out **where every instruction lands** in memory — so it knows the value of every **label**.
2. The object code then has those **label addresses encoded** into the bytes.

### Memory-filling commands
| Command | Effect | Example |
|---------|--------|---------|
| `label:` | name the current memory address | `loop:` |
| `.pos N` | move the current address to `N` | `.pos 0x70` |
| `.align N` | advance to the next multiple of `N` (for alignment) | `.align 8` |
| `.quad V` | place a 64-bit value at the current address | `.quad 0xabcd` |

Try the online simulator (used in Lab 8): `https://boginw.github.io/js-y86-64/`

## Common Pitfalls & Misconceptions

- **Reading the operand order backwards.** In `subq rA, rB` the result goes in **`rB`** and it computes `rB − rA` (not `rA − rB`). The **destination is the second operand** and is overwritten. The same "second operand is destination" rule holds for `addq`/`andq`/`xorq`/`rrmovq`.
- **Forgetting the `$` on immediates.** `irmovq 0x54, %rcx` is wrong; immediates need `$`: `irmovq $0x54, %rcx`. Without `$`, a bare number is treated as a memory address/displacement.
- **Trying memory → memory in one step.** There is no such instruction; load into a register with `mrmovq`, then store with `rmmovq`.
- **Expecting moves to set flags.** Only the **ALU ops** set the condition codes. A `mov` of a zero value does **not** set ZF — so put the flag-setting op (often `andq`/`subq`) right before the jump that reads it.
- **Off-by-displacement in `D(rB)`.** `mrmovq 8(%rdi), %rax` reads from address `8 + %rdi`, **not** from `%rdi` and then index 8. The displacement is in **bytes**.

## Key Takeaways

- Assembly = human-readable machine code that manipulates **processor state**: memory, 15 registers, PC, condition codes, status.
- Y86-64 is a small, quad-word-only **subset** of real X86-64.
- **Moves** can't go memory-to-memory; **ALU ops** overwrite their destination and **set the flags**.
- **Condition codes** + **conditional jumps** give you control flow.
- The simulator **fills memory** from your source; `label:`, `.pos`, `.align`, `.quad` control placement.

## Self-Check

1. Which single register is *not* general-purpose, and what does it hold?
2. Why is there no single instruction to copy from one memory location directly to another?
3. After `subq %rax, %rbx` where `%rax == %rbx`, what are ZF, SF, OF?
4. Write Y86-64 to compute `0x10 - 0x3` into `%rdx`.
5. What two passes does the simulator make, and why does it need the first one?