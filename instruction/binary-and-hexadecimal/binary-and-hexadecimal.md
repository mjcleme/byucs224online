# Binary & Hexadecimal

## Learning Objectives

After this lesson you should be able to:
- Convert fluently among **decimal, binary, and hexadecimal**.
- Convert **binary ↔ hex** by grouping/expanding 4 bits, *without* going through decimal.
- Apply the **divide-by-2** algorithm to convert decimal → binary, and explain *why* it works.
- Add two binary numbers with carries, and use **shifts** (`<<`, `>>`) to multiply/divide by powers of 2.

> **Fluency goal:** you should eventually recognize the bit patterns for hex digits `0–F` on sight (e.g., `B = 1011`). This pays off constantly when reading memory dumps, assembly, and cache problems.

## Place Value: A First-Grade Idea, Generalized

How do we know the value of a number like **105624**? Each digit's value depends on its **place**.

In **base 10 (decimal)**:

| Digit | Place value | Contribution |
|-------|-------------|--------------|
| 4 | 1 | 4 |
| 2 | 10 | 20 |
| 6 | 100 | 600 |
| 5 | 1000 | 5000 |
| 0 | 10000 | 0 |
| 1 | 100000 | 100000 |

Sum = **105624**.

> **The rule:** in base *b*, the *i*-th place has value **b^i**.
> - Base 10: place 0 = 1, place 1 = 10, place 2 = 100, …
> - This works for **any** base.

We will use **base 2 (binary)** and **base 16 (hexadecimal)**.

## Binary Place Values (base 2)

| Place | 8 | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
|-------|---|---|---|---|---|---|---|---|---|
| Value | 256 | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |

**To read a binary number, add up the place values where there's a 1:**

| Binary | Computation | Value |
|--------|-------------|-------|
| `1010011` | 64 + 16 + 2 + 1 | **83** |
| `110` | 4 + 2 | **6** |
| `10111` | 16 + 4 + 2 + 1 | **23** |
| `0110101` | 32 + 16 + 4 + 1 | **53** |

## Hexadecimal Place Values (base 16)

| Place | 4 | 3 | 2 | 1 | 0 |
|-------|---|---|---|---|---|
| Value | 65,536 | 4,096 | 256 | 16 | 1 |

Hex uses 16 symbols. Remember the letters:
**A = 10, B = 11, C = 12, D = 13, E = 14, F = 15**

| Hex | Computation | Value |
|-----|-------------|-------|
| `12` | (16 × 1) + 2 | **18** |
| `3a1` | (256 × 3) + (16 × 10) + 1 | **929** |
| `bf` | (16 × 11) + 15 | **191** |
| `c57` | (256 × 12) + (16 × 5) + 7 | **3159** |

## Why Hexadecimal? The Nibble Connection

Consider **4 bits** (a **nibble**, half a byte). Four bits map *exactly* onto one hex digit:

| Binary | Dec | Hex | | Binary | Dec | Hex |
|--------|-----|-----|---|--------|-----|-----|
| 0000 | 0 | 0 | | 1000 | 8 | 8 |
| 0001 | 1 | 1 | | 1001 | 9 | 9 |
| 0010 | 2 | 2 | | 1010 | 10 | A |
| 0011 | 3 | 3 | | 1011 | 11 | B |
| 0100 | 4 | 4 | | 1100 | 12 | C |
| 0101 | 5 | 5 | | 1101 | 13 | D |
| 0110 | 6 | 6 | | 1110 | 14 | E |
| 0111 | 7 | 7 | | 1111 | 15 | F |

Hex is just a **compact, human-readable shorthand for binary** — perfect for showing memory.

### Binary → Hex: group bits into 4s (from the right), convert each group

```
0100 1011 1011 0101 0010   →   0x4bb52
   1 1101 0010 0001        →   0x1d21
```

### Hex → Binary: expand each hex digit to 4 bits

```
51a4b   →   0101 0001 1010 0100 1011
42ff    →   0100 0010 1111 1111
```

## Decimal → Binary: the Divide-by-2 Algorithm

```
Until n == 0:
    next bit = n % 2     // produces bits least-significant first
    n = n / 2
```

**Example: convert 43 to binary**

| Step | n % 2 (bit) | n / 2 |
|------|-------------|-------|
| 43 | 1 | 21 |
| 21 | 1 | 10 |
| 10 | 0 | 5 |
| 5 | 1 | 2 |
| 2 | 0 | 1 |
| 1 | 1 | 0 |

Reading the bits **bottom-to-top**: `101011`.

### Why this works (the bit-level view)

If `n = 1011` (decimal 11):
- **`n % 2`** gives the **least significant bit** — it's how we tell even (0) from odd (1).
- **`n / 2`** is just a **right shift by 1** in binary (`1011` → `0101`).

So the whole algorithm is: *look at the last bit, then shift right*, repeated until nothing is left.

## Arithmetic in Other Bases

### Addition
Works exactly like base 10: add a column, carry out to the next. Binary has few cases:

| Add | Result | Carry |
|-----|--------|-------|
| 0 + 0 | 0 | 0 |
| 0 + 1 | 1 | 0 |
| 1 + 0 | 1 | 0 |
| 1 + 1 | 0 | 1 |

With a carry-in (3 bits):
- 0+0+0 = 0 (carry 0)
- 0+0+1 = 1 (carry 0)
- 0+1+1 = 0 (carry 1)
- 1+1+1 = 1 (carry 1)

Worked example: `10110 + 11011 = 110001`.

### Multiplying / dividing by the base = shifting

- In **base 10**, ×10 just appends a 0: `572 × 10 = 5720`.
- In **binary**, ×2 appends a 0: `1011 × 10₂ = 10110`.

> This shift is done in hardware as a **bit shift**, written in C as `<<`:
> - `a << 2` shifts the bits of `a` **two places left**; empty spots get 0. **Left shift by k = multiply by 2ᵏ.**

Division by the base shifts the **other way** (and drops the remainder):
- `562 / 10 = 56` (decimal)
- `1011 / 10₂ = 0101` (i.e. 11 / 2 = 5)
- In C: `a >> k` is a **right shift = divide by 2ᵏ** (e.g. `a >> 3` divides by 8).

## Common Pitfalls & Misconceptions

- **Grouping bits from the wrong end.** When converting binary → hex, group into 4s **starting from the right** (the least significant bit). If the leftmost group is short, pad it with leading zeros: `1 1101 0010` → `0001 1101 0010` → `0x1d2`.
- **Reading the divide-by-2 result top-to-bottom.** The remainders come out **least-significant-bit first**, so the answer is read **bottom-to-top**. Reading top-to-bottom reverses the number.
- **Confusing a hex digit with a byte.** One hex digit = **4 bits** (a nibble); **two** hex digits = **1 byte**. `0xFF` is one byte (255), not two.
- **Forgetting what shifting drops.** A right shift `>>` is integer division by a power of 2 and **discards the remainder** (`1011 >> 1 = 0101`, i.e., 11 → 5). A left shift can **overflow** out the top of a fixed-width type.
- **Assuming `0x`/`0b` are stored.** Prefixes like `0x` are just notation for humans; in memory there are only bits. The *same* bits can be printed as decimal, hex, or binary.

## Key Takeaways

- A digit's value = (digit) × (base^place). This rule is the same in every base.
- **1 hex digit = 4 bits = 1 nibble**, which is why hex is the natural shorthand for binary.
- Convert binary↔hex by grouping/expanding 4 bits at a time.
- Convert decimal→binary with repeated `% 2` (gives next bit) and `/ 2` (shift right).
- `<<` multiplies by powers of 2; `>>` divides by powers of 2.

## Self-Check

1. What is `0x2F` in decimal? In binary?
2. Convert `1101110` (binary) to hex without converting to decimal first.
3. Convert decimal 100 to binary using the divide-by-2 algorithm.
4. What is `5 << 3` in decimal? What is `40 >> 2`?
5. Why is `1 + 1 = 10` in binary, not `2`?

---
*Course note (Project 1, "myxxd"):* read bytes from a file and print them in hex, binary, and as characters (when printable). Formatting must match `xxd` exactly. Tip: to print a byte as two hex digits with a leading zero, use the format string `%02x`.