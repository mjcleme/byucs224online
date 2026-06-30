# Bit Operations

## Learning Objectives

After this lesson you should be able to:
- Apply the **bitwise operators** `& | ^ ~ << >>` and predict their results bit-by-bit.
- Distinguish **logical** (`>>` zero-fill) from **arithmetic** (`>>` sign-fill) right shifts.
- Use a **mask** to **extract** bits (AND) or **set** bits (OR), and build masks with `~0` and shifts.
- Tell apart **bitwise** (`&`) and **logical** (`&&`) operators and know when each is correct.
- Solve "bit-level only" problems under the textbook's restricted-operator rules.

> **Mental model for masking:** decide, for each bit, whether you want to **clear it (→0)**, **set it (→1)**, or **leave it alone**. Then: *AND clears, OR sets,* and AND-with-1 / OR-with-0 leaves bits untouched. Every masking problem reduces to this table.

## Recall: Shifting

Multiplying a binary number by 2 appends a 0 on the right (`1011 × 10₂ = 10110`). In hardware/C this is a **bit shift**:
- `a << 2` shifts the bits of `a` two places **left**; empty spots get **0**. (Left shift by *k* = multiply by 2ᵏ.)

Left shift is one of several **bitwise operators** that are essential for low-level work.

## The Bitwise Operators

| Operator | C symbol | Example |
|----------|----------|---------|
| Left shift | `<<` | `a << 2` |
| Right shift | `>>` | `a >> 3` |
| AND | `&` | `a & b` |
| OR | `\|` | `a \| b` |
| XOR | `^` | `a ^ b` |
| NOT | `~` | `~a` |

**Two kinds of right shift:**
- **Logical** — empty (high) spots get **0**.
- **Arithmetic** — empty (high) spots **repeat the original sign bit** (used for signed values, to preserve sign).

## Truth Tables

| A | B | A&B | A\|B | A^B |   | A | ~A |
|---|---|-----|------|-----|---|---|----|
| 0 | 0 | 0 | 0 | 0 |   | 0 | 1 |
| 0 | 1 | 0 | 1 | 1 |   | 1 | 0 |
| 1 | 0 | 0 | 1 | 1 |   |   |   |
| 1 | 1 | 1 | 1 | 0 |   |   |   |

- **AND**: 1 only if **both** are 1.
- **OR**: 1 if **at least one** is 1.
- **XOR**: 1 if they **differ**.
- **NOT**: flips each bit.

### Bitwise operators work on each bit independently

Worked example (4-bit halves shown; full byte in the slides):
```
A    = 0110 1101   (0x6d)
B    = 1100 0101   (0xc5)
A&B  = 0100 0101   (0x45)
A|B  = 1110 1101   (0xed)
A^B  = 1010 1000   (0xa8)
~A   = 1001 0010   (0x92)
```

## Using Bitwise Operators: Masks

Bitwise ops are used for two main jobs:
1. **Extracting** some bits from a value.
2. **Setting** some bits of a value.

A **mask** is a value that marks *which* bits we care about. This matters whenever individual bits (or small groups) carry meaning — e.g., each bit is the on/off **status** of part of a larger system.

### The fundamental rules

| Operation | Effect on a bit |
|-----------|-----------------|
| **AND with 0** | sets the bit to **0** |
| **AND with 1** | leaves the bit **unchanged** |
| **OR with 0** | leaves the bit **unchanged** |
| **OR with 1** | sets the bit to **1** |

For any single bit you have three goals — and now you know how to achieve each:
- **Set to 0** → AND with 0
- **Set to 1** → OR with 1
- **Leave unchanged** → AND with 1 (or OR with 0)

## Extracting Bits

- **Desired bits:** unchanged. **Other bits:** set to 0.
- **Tool:** `AND` with a mask that has **1 in the desired positions, 0 elsewhere.**

```c
char c = 0101 1001;   // we want bits 0–3 (the low nibble, 1001)
char m = 0000 1111;   // mask
c & m  = 0000 1001;   // result: just the bits we wanted, rest zeroed
```

## Setting Bits

- **Desired bits:** set to 1. **Other bits:** unchanged.
- **Tool:** `OR` with a mask that has **1 in the desired positions, 0 elsewhere.**

```c
char c = 0111 0001;   // we want to set bits 0–3 to 1
char m = 0000 1111;   // mask
c | m  = 0111 1111;
```

## Building Masks Portably

Don't hardcode a width — `0xFFFF` only gives all-1s for a 16-bit value.
- **`~0`** gives **all 1s** for **any word size** (flip all bits of 0).
- You then **shift** the mask bits into the position you need.
- To get the word size `w` in bits: `sizeof(int) << 3` (i.e. bytes × 8).

## Bitwise vs. Logical Operators

Don't confuse the **bitwise** operators (`&`, `|`, `~`) with the **logical** operators:

| Logical | C symbol |
|---------|----------|
| AND | `&&` |
| OR | `\|\|` |
| NOT | `!` |

Logical operators treat each argument as **True (non-zero)** or **False (zero)** and produce **1 or 0**.

```c
int x = 1, y = 224, z = 0;
x && y  // 1     x || y  // 1
x && z  // 0     x || z  // 1
y && z  // 0     y || z  // 1
!x // 0   !y // 0   !z // 1
```

## Bit-Level Coding Rules (from the textbook)

Many exercises ask you to solve problems using **only** bit-level tools, for any int size (a multiple of 8 — use `sizeof(int) << 3` for the word size `w`):

**Not allowed:** conditionals (`if`, `?:`), loops, `switch`, functions, macros; division, modulus, multiplication; relative comparisons (`<`, `>`, `<=`, `>=`); equality/inequality (`==`, `!=`).

**Allowed:** bit-level and logic operations; left/right shifts with amounts in `[0, w−1]`; addition and subtraction; the constants `INT_MIN` and `INT_MAX`; casting between `int` and `unsigned`.

### Practice (textbook 2.61)
Write C expressions that evaluate to **1** when true, **0** when false (`x` is an `int`):
- Any bit of `x` equals 1
- Any bit of `x` equals 0
- Any bit in the **least significant byte** of `x` equals 1
- Any bit in the **most significant byte** of `x` equals 0

### Extra practice (use only 1-byte constants; `x` is a 32-bit int)
- Return only the **6th nibble** of `x` (e.g., `0x12345678` → `0x3`).
- Set the **11th and 12th bits** of `x` to 1, leaving the rest unchanged (`0x12345678` → `0x12345e78`).
- Clear (set to 0) all 4 bits of the **2nd nibble** (`0x12345678` → `0x12345608`).
- Return the **4th byte** (7th & 8th nibbles) as a `char` (`0x12345678` → `0x12`).

## Common Pitfalls & Misconceptions

- **`&` vs `&&` (and `|` vs `||`).** `5 & 2 == 0` (bitwise: `0101 & 0010`), but `5 && 2 == 1` (logical: both nonzero → true). Using the wrong one is a classic silent bug. Bitwise works on **each bit**; logical works on the **whole value's truthiness**.
- **Hardcoding mask widths.** `0xFFFF` is all-ones only for 16 bits. Use **`~0`** for an all-ones mask of any width, then shift it into place.
- **Shifting by too much / negative amounts.** Shifting an N-bit value by ≥ N (or by a negative amount) is **undefined behavior** in C. Keep shift amounts in `[0, w-1]`.
- **Right-shifting signed values.** `>>` on a signed negative number is **arithmetic** (copies the sign bit), so `-8 >> 1` is `-4`, not a big positive number. If you want zero-fill, operate on an `unsigned`.
- **Precedence of bitwise operators.** `&`, `|`, `^` bind **looser** than `==`. `x & 1 == 0` parses as `x & (1 == 0)` = `x & 0`. Parenthesize: `(x & 1) == 0`.

## Key Takeaways

- `&`, `|`, `^`, `~`, `<<`, `>>` operate **bit-by-bit**.
- **AND clears** (mask 0), **OR sets** (mask 1); AND-with-1 / OR-with-0 leaves bits alone.
- **Extract** with `AND` + mask; **set** with `OR` + mask.
- Build width-independent masks with **`~0`** and shifts.
- `&&`/`||`/`!` are **logical** (whole-value true/false), not bitwise.

## Self-Check

1. Compute `0x3C & 0x0F`, `0x3C | 0xF0`, and `~0x3C` (8-bit).
2. Write a mask and operation to **clear** the top 4 bits of a byte while leaving the bottom 4 unchanged.
3. Why use `~0` instead of `0xFFFFFFFF` for an all-ones mask?
4. What's the difference between `x & y` and `x && y` when `x = 2` and `y = 1`?
5. Give a C expression that returns the 6th nibble of a 32-bit `x`.