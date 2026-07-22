# Attacking the Stack: Buffer Overflows, Attacks & Defenses

*Learning material derived from `13a AttackingTheStack.pptx`*

> **📍 Course context (Winter 2026) — Unit 13: Attack Project.**
> **Read:** CS:APP Chapter 3, especially **§3.10**.
> **Supports:** Unit 13 Lab Prep & Lab 13: Attacking with ROP (due **Fri Apr 3**) · Project 5: Attack (due **Fri Apr 3**).
> See the [course schedule](SCHEDULE.md) and [assignment list](ASSIGNMENTS.md).

> **Context:** This material is for the **Attack Lab** in an authorized course setting — understanding these attacks makes you a more **defensive** programmer. The goal is education and defense, not malicious use.

## Learning Objectives

After this lesson you should be able to:
- Explain how an **unchecked input** (e.g., `gets`) overflows a buffer and overwrites the **saved return address**.
- Distinguish **control-flow redirection** (Attack 1) from **code injection** (Attack 2).
- Describe the major **defenses** — defensive coding, stack randomization (ASLR), stack canaries, and a non-executable stack — and what each one stops.
- Explain **return-oriented programming (ROP)**: what a **gadget** is and why ROP defeats a non-executable stack.

## The Setup: A Vulnerable Program

A typical situation: a program reads input from the user. Our task in the Attack Lab is to **craft special input to attack the program**. These are **buffer overflow attacks.**

The classic vulnerability is **`gets`**:
- `gets` reads characters from stdin into memory starting at `buffer`.
- **It does NOT check the size of the input** — so input longer than the buffer **overflows** into whatever is next in memory.

## How the Stack Gets Corrupted

When a function with a local `buffer` runs, the stack (growing down) looks like:

```
higher addr   | Return address in main      |
              | Saved %rbp                  |
              | Ret. addr. in call_echo     |
              | Saved %rbp                  |
lower addr    | buffer  <- %rsp             |
```

`gets` writes input into `buffer` and keeps going **upward** (toward higher addresses) as the input grows:

- Input `"abc"` → fits in the buffer, null-terminated. Fine.
- Input `"abcdefg"` → starts filling toward the saved `%rbp`. Getting close.
- A **20-byte input** → overruns the buffer, the saved `%rbp`, **and the return address**.

> **Key idea:** If bytes 12–19 of the input land on the saved **return address**, then when the function executes `ret`, it will **return to whatever address the attacker wrote** — *not* back to `call_echo`.

So a carefully built input becomes:
```
| return address (overwritten → attack function addr) |
| input bytes 5–11                                    |
| input bytes 0–4   <- buffer                         |
```

## Attack 1: Redirect Control Flow

Overwrite the saved return address with the **address of an existing function** you want to run. On `ret`, control jumps there.

## Attack 2: Code Injection

Go further — **inject your own machine instructions** as part of the input string:

1. **Write exploit code** (the bytes of the instructions you want to run).
2. **Determine the address** those bytes will have on the stack.
3. **Overwrite the return address** with that stack address.
4. Now `ret` jumps **into your injected code on the stack** and starts executing it.

## Defenses

Buffer overflow attacks overwrite stack values in creative ways. How do we defend?

### 0. Better programming (first line of defense)
Knowing these attacks exist helps you **program defensively** — e.g., use length-checking functions (`fgets`) instead of `gets`. What does `gcc` add on top?

### 1. Stack Randomization (ASLR)
Many attacks need to **know the address** of things on the stack to hardcode into the exploit string. **Stack randomization** places the stack in a **different position on each run**, so a hardcoded address won't reliably work.

### 2. Stack Corruption Detection (Canaries)
Can we **detect** corruption? Insert a randomly generated **canary value** onto the stack in any function that reads user input. **Check it before returning.** If the canary changed, the stack was corrupted → **stop with an error.**

### 3. Limiting Executable Regions (NX / W^X)
Code injection relies on **executing instructions stored on the stack** — an odd place for code! The stack can be marked **non-executable**, so setting the PC into it raises an error. This defeats Attack 2.

## The Final Attack: Return-Oriented Programming (ROP)

If the stack is non-executable, can we still "inject" behavior? **Yes — with ROP.**

> **Key idea:** Build a program out of **bytes of the *target* program itself.** Since the program's own instructions live in **executable** memory, we reuse them.

The trick centers on the **`ret`** instruction: it **pops an address off the stack and sets the PC** to it, then executes from there. A **gadget** is a short useful instruction sequence that **ends in `ret`**.

By overflowing the stack with a **chain of gadget addresses** (plus any needed data), each gadget runs and its trailing `ret` pops the **next** gadget address — chaining them into a program:

```
Stack (read upward as ret pops each):
  address of target code
  address of gadget 3
  data for gadget 2
  address of gadget 2
  address of gadget 1   <- rsp starts here
  ... (buffer overflow start) ...
```

### Where do gadgets come from?
We look for **bytes that correspond to useful instructions followed by a `ret`.** These bytes need not be intended instructions — they can come from:
- **strings** encoded in the program,
- **immediate/constant** values,
- **inside the encodings** of other instructions,
- or the actual instructions.

### Building a ROP attack (end product)
1. **Find the gadgets** available in executable memory.
2. **Assemble a sequence** of them that does something useful.
3. **Craft the exploit string** placing the gadget addresses (and any data, e.g., for `popq`) on the stack in order.

You'll perform variants of all of these in the **Attack Project.**

## Common Pitfalls & Misconceptions

- **Why the *return address*, not the buffer, is the target.** Overwriting the buffer alone just changes data. The attack works because the saved **return address sits at a higher address than the buffer**, so an overflow flows *up* into it — and `ret` blindly trusts whatever is there.
- **Byte order of the injected address.** The address you write into the exploit string must be in **little-endian** byte order (least significant byte first), or `ret` will jump somewhere unintended.
- **Canaries don't stop overflows — they detect them.** A canary catches corruption **at return time** and aborts; it doesn't prevent the write. ASLR and a non-executable stack address *different* parts of the attack.
- **A non-executable stack doesn't end exploitation.** It blocks **code injection** (Attack 2) but not **ROP**, which reuses the program's *existing* executable bytes — so no new code runs on the stack.
- **"Gadget" ≠ a whole function.** A gadget is a short byte sequence ending in `ret`, and it may not even be an intended instruction — it can come from the middle of an instruction, a string, or a constant.

## Key Takeaways

- **Unchecked input (`gets`)** lets input overflow a buffer and **overwrite the saved return address** → attacker controls where `ret` goes.
- **Attack 1**: redirect to an existing function. **Attack 2**: inject and execute new code on the stack.
- Defenses: **defensive coding**, **stack randomization**, **canaries**, and **non-executable stack**.
- **ROP** defeats a non-executable stack by chaining **gadgets** (existing code ending in `ret`) via a stack full of gadget addresses.


```masteryls
{"id":"2576be4f-2e34-43b0-aacc-eba5fe18de71", "title":"Essay", "type":"essay", "gradingCriteria":"- Addresses the prompt directly\n- Uses at least one concrete example\n- Demonstrates accurate understanding of key concepts" }
Why is `gets` dangerous, and what makes it possible to overwrite the return address?
```
```masteryls
{"id":"82939e5d-1dec-48d7-8264-989b30fc79d9","title":"Essay","type":"essay","gradingCriteria":"- Addresses the prompt directly\n- Uses at least one concrete example\n- Demonstrates accurate understanding of key concepts"}
Explain the difference between Attack 1 (control redirection) and Attack 2 (code injection).
```
```masteryls
{"id":"8238d7c9-1844-4a99-8c57-53f27594ce49","title":"Essay","type":"essay","gradingCriteria":"- Addresses the prompt directly\n- Uses at least one concrete example\n- Demonstrates accurate understanding of key concepts"}
Which defense specifically defeats code injection on the stack, and why?
```

```masteryls
{"id":"fb1ca465-c094-47ec-84c0-cb3b280332d4", "title":"Teaching", "type":"teaching" }
Help me understand how does a stack canary detect an overflow?
```

```masteryls
{"id":"88bf1713-8e63-4a2e-88d7-21336e27d55a","title":"Teaching","type":"teaching"}
Why does ROP still work even when the stack is non-executable? 
```

```masteryls
{"id":"ba692aca-0c03-4164-a5d0-bc8c121d6594","title":"Essay","type":"essay","gradingCriteria":"- Addresses the prompt directly\n- Uses at least one concrete example\n- Demonstrates accurate understanding of key concepts"}
What is a "gadget"?
```