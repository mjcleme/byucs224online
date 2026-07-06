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

In low-level programming, you often work with a `char *` buffer representing raw bytes from a file. While the buffer is just a sequence of bytes, specific offsets within that buffer may represent larger types like 4-byte integers or 2-byte shorts.

Suppose you have `char *p` pointing into a buffer of bytes, and four of those bytes (starting 8 bytes past `p`) actually encode an `int` you need.

### The wrong way
This example attempts to extract the integer by simply adding an offset to the character pointer and dereferencing it.
```c
int needed = *(p + 8);
```
**Why it fails:** Because `p` is a `char *`, the compiler treats `p + 8` as the address of a single character. When you dereference it with `*`, the CPU reads only **one byte**. Even though you assign it to an `int`, the damage is done: you only captured 1/4th of the data, and the remaining 3 bytes are ignored.

### The right way: make a pointer of the correct type, then dereference
To fix this, you must tell the compiler to treat that specific memory address as the start of an integer. This involves casting the address to an `int *` before the dereference happens.

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

To demonstrate overflow without using massive numbers, we use a hypothetical 3-bit unsigned type called a **`tryte`**. This type has a very low "ceiling," making it easy to see where math breaks down.
- Smallest = `000` = 0, Largest = `111` = 7.

The following function looks logically sound but contains a hidden bug:
```c
tryte average(tryte a, tryte b) {
    tryte c = a + b;
    return c / 2;
}
```

**Does it work?** It depends on the input values:
- **Success:** `a = 2 (010)`, `b = 4 (100)` → `a+b = 110 (6)`. Since 6 fits in 3 bits, `6 / 2 = 3` works correctly. ✓
- **Failure:** `a = 5 (101)`, `b = 3 (011)` → `a+b` should be `8 (1000)`. However, an 8 requires **4 bits**. In our 3-bit `tryte`, the high bit is lost, and the value "wraps" to `000 (0)`. Consequently, `0 / 2 = 0`. ✗ **Wrong** — the sum overflowed *before* the division could happen.

### Fix 1: do the risky part in a bigger type
The first solution is to manually promote the variables to a wider type (like a 4-bit `quyte`) so the intermediate sum has enough "room" to exist without wrapping.
```c
tryte average(tryte a, tryte b) {
    quyte A = (quyte)a;
    quyte B = (quyte)b;
    quyte C = A + B;          // no overflow — 4 bits is enough to hold 8
    quyte result = C / 2;
    return (tryte)result;     // safe to narrow back to 3 bits now
}
```

The same logic can be written more compactly. This version performs the same explicit casts but in a single line:
```c
tryte average(tryte a, tryte b) {
    return (tryte)((((quyte)a) + ((quyte)b)) / 2);
}
```

### Fix 2: let C's default integer promotion help
C has a built-in rule: when performing arithmetic on types smaller than an `int`, it automatically promotes them to `int` for the duration of the calculation.
```c
tryte average(tryte a, tryte b) {
    return (a + b) / 2;
}
```
This works because in a **single-line expression**, the addition `a + b` is performed using standard 32-bit `int` math. Since 32 bits is plenty of room for our small numbers, the overflow is avoided. The result is only truncated back to the narrow type during the final `return`.

> **Takeaway:** know the width of the type your *intermediate results* live in — overflow happens at the operation, not at the final assignment.

## Project Logistics (BMP Filter)

- Similar in length to Project 1 (reference solution ~40 lines), with slightly more context to understand.
- Plan **at least as much time** as Project 1 took. **Start early.**
- Use lab time to understand the problem, the code structure, and the project's two components.

## Common Pitfalls & Misconceptions

When working with these pointer and math patterns, keep these specific nuances in mind:

- **Operator precedence in `*(int *)(p+8)`.** The cast applies to the address `(p+8)` *first*, then the leading `*` dereferences. Writing `*(int *)p + 8` instead adds 8 to the *value* retrieved from the pointer—very different. Keep the parentheses around the cast and the address.
- **Pointer arithmetic is scaled by type.** `p + 8` on a `char *` advances 8 **bytes**, but `q + 8` on an `int *` advances `8 × 4 = 32` bytes. When you need an exact byte offset from a file specification, always perform the math on a `char *` first.
- **Assuming the final type catches overflow.** In the line `tryte c = a + b;`, the overflow happens *during* the addition because `c` is narrow. Simply making `c` a wider type (`int c = a + b;`) doesn't help if `a` and `b` are still treated as narrow types during the addition. You must widen the **operands**.
- **Alignment when casting.** On some systems, reading an `int` from an address that isn't 4-byte aligned (e.g., an odd address) causes a hardware fault. For the BMP project, the file offsets are carefully arranged to work, but in general, you cannot assume any `char *` can be safely reinterpreted as a wider type.

## Key Takeaways

- To read a multi-byte value from a byte buffer: `*(int *)(address)` — cast then dereference.
- Dereferencing a `char *` only reads **one byte**, regardless of what's stored there.
- **Overflow** strikes intermediate results; widen the type (or rely on C's `int` promotion in a single expression) to prevent it.

## Self-Check

1. Why does `*(p + 8)` (with `char *p`) give the wrong value when you want a 4-byte int? Fix it.
2. In the first `tryte average`, identify the exact step where overflow occurs for `a=5, b=3`.
3. Why does `return (a + b) / 2;` avoid overflow even though `a` and `b` are narrow types?