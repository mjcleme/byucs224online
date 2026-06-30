# Tips for Project 2 (BMP Filter) — Pointers & Overflow

## Learning Objectives

After this lesson you should be able to:
- Read a **multi-byte typed value** out of a raw byte buffer using the `*(TYPE *)(address)` pattern.
- Explain why dereferencing a `char *` reads only **one byte**, and fix code that does this by mistake.
- Identify where **overflow** can occur in an arithmetic expression (it happens at the *operation*, not the assignment).
- Prevent overflow by computing intermediate results in a **wider type**.

---

This session gives practical tools you'll need for **Project 2 (BMP Filter)**: pulling typed values out of raw memory with pointers, and avoiding **overflow** in arithmetic.

## Getting Typed Values Out of Raw Memory

Suppose you have `char *p` pointing into a buffer of bytes, and four of those bytes (starting 8 bytes past `p`) actually encode an `int` you need.

### The wrong way
```c
int needed = *(p + 8);
```
**Why it fails:** `p` is a `char *`, so `*(p + 8)` dereferences **one byte** (a `char`) and then widens that single byte to an `int`. You get only 1 of the 4 bytes you wanted.

### The right way: make a pointer of the correct type, then dereference
```c
int *n = (int *)(p + 8);   // reinterpret that address as "pointer to int"
int needed = *n;           // dereference reads all 4 bytes
```

Or, more compactly, in one line:
```c
int needed = *(int *)(p + 8);
```

> **Pattern to memorize:** `*(TYPE *)(address)` — cast the address to a pointer of the type you want, *then* dereference. This is exactly how you read multi-byte fields out of a file/buffer (the core skill in the BMP project).

## Overflow

> **Overflow:** the result of a math operation doesn't fit in the type being used.
> Result: **subtle errors** and incorrect code that *looks* right.

### Example: averaging two numbers

Imagine a made-up 3-bit unsigned type called a **`tryte`**:
- Smallest = `000` = 0, Largest = `111` = 7.

```c
tryte average(tryte a, tryte b) {
    tryte c = a + b;
    return c / 2;
}
```

**Does it work?** Sometimes:
- `a = 2 (010)`, `b = 4 (100)` → `a+b = 110 (6)` → `/2 = 011 (3)` ✓
- `a = 5 (101)`, `b = 3 (011)` → `a+b` should be `1000 (8)`, **but that needs 4 bits!** In a 3-bit `tryte` it wraps to `000 (0)` → `/2 = 0`. ✗ **Wrong** — the sum overflowed *before* the division.

### Fix 1: do the risky part in a bigger type
Use a wider type (call it a 4-bit `quyte`) for the intermediate sum:
```c
tryte average(tryte a, tryte b) {
    quyte A = (quyte)a;
    quyte B = (quyte)b;
    quyte C = A + B;          // no overflow — 4 bits is enough
    quyte result = C / 2;
    return (tryte)result;     // safe to narrow now
}
```

The same logic written compactly (the compiler produces essentially the same code — prefer the readable version):
```c
tryte average(tryte a, tryte b) {
    return (tryte)((((quyte)a) + ((quyte)b)) / 2);
}
```

### Fix 2: let C's default integer promotion help
```c
tryte average(tryte a, tryte b) {
    return (a + b) / 2;
}
```
This works because in a **single-line expression**, C performs the arithmetic using `int`s (which are wide enough here), and the compiler is smart enough to avoid the intermediate overflow before assigning back to the narrow type.

> **Takeaway:** know the width of the type your *intermediate results* live in — overflow happens at the operation, not at the final assignment.

## Project Logistics (BMP Filter)

- Similar in length to Project 1 (reference solution ~40 lines), with slightly more context to understand.
- Plan **at least as much time** as Project 1 took. **Start early.**
- Use lab time to understand the problem, the code structure, and the project's two components.

## Common Pitfalls & Misconceptions

- **Operator precedence in `*(int *)(p+8)`.** The cast applies to the address `(p+8)` *first*, then the leading `*` dereferences. Writing `*(int *)p + 8` instead adds 8 to the *value* — very different. Keep the parentheses.
- **Pointer arithmetic is scaled by type.** `p + 8` on a `char *` advances 8 **bytes**, but `q + 8` on an `int *` advances `8 × 4 = 32` bytes. When you need an exact byte offset, do the math on a `char *`.
- **Assuming the final type catches overflow.** `tryte c = a + b;` overflows *during* `a + b` (in the narrow type), before it's ever stored. Widening the *result variable* doesn't help; you must widen the **operands**.
- **Alignment when casting.** On some systems, reading an `int` from an address that isn't 4-byte aligned faults. For the BMP project the offsets are arranged to work, but don't assume any `char *` can be reinterpreted as any wider type.

## Key Takeaways

- To read a multi-byte value from a byte buffer: `*(int *)(address)` — cast then dereference.
- Dereferencing a `char *` only reads **one byte**, regardless of what's stored there.
- **Overflow** strikes intermediate results; widen the type (or rely on C's `int` promotion in a single expression) to prevent it.

## Self-Check

1. Why does `*(p + 8)` (with `char *p`) give the wrong value when you want a 4-byte int? Fix it.
2. In the first `tryte average`, identify the exact step where overflow occurs for `a=5, b=3`.
3. Why does `return (a + b) / 2;` avoid overflow even though `a` and `b` are narrow types?