# Integer Representations (Signed & Unsigned)

## Learning Objectives

After this lesson you should be able to:
- Evaluate the value of a bit pattern as both an **unsigned** and a **two's-complement** integer.
- State the **range** of each signed and unsigned type and why signed ranges are asymmetric.
- **Negate** a two's-complement number (flip bits, add 1) and explain the `TMin` "own negative" anomaly.
- Predict the result of **converting** between signed/unsigned and between sizes (**truncation**, **zero-** vs **sign-extension**).
- Explain why **mixing signed and unsigned** in one expression is dangerous.

> **The one rule that explains most of this lesson:** *changing how you label bits never changes the bits.* Signed↔unsigned conversion reinterprets; only truncation/expansion add or remove bits.

## Unsigned Integers

Every integer type (everything except `float`/`double`/pointers) can be **unsigned**: `unsigned char`, `unsigned short`, `unsigned int`, `unsigned long`.

Unsigned values are encoded as **normal binary** (place values, as covered previously).

- **Smallest** value = all bits 0.
- **Largest** value = all bits 1.

| Type | Range |
|------|-------|
| `unsigned char` | [0, 255] |
| `unsigned short` | [0, 65,535] |
| `unsigned int` | [0, 4,294,967,295] |
| `unsigned long` | [0, 18,446,744,073,709,551,615] |

**Addition** is done the normal way (add places, carry out, possible overflow) and is easy to build directly into circuits.

## Signed Numbers: The Design Problem

We also need **negative** numbers. But the number of bit patterns is **fixed** (2ⁿ for n bits) — so to gain negatives we must give up some positives. We need a *code*: a rule mapping bit patterns to signed values.

A **good** representation would have:
1. **No wasted values** (no two codes for the same number).
2. **Easy negation.**
3. **The same addition circuitry** as unsigned (so we don't need separate hardware).

We'll explore using **4 bits** (16 patterns) to keep things simple; the same ideas scale to real types.

## First Try: Sign-Magnitude

Idea: the **first bit is the sign**; the rest is the magnitude.

| Code | SM value | | Code | SM value |
|------|----------|---|------|----------|
| 0000 | 0 | | 1000 | **−0** |
| 0001 | 1 | | 1001 | −1 |
| 0010 | 2 | | 1010 | −2 |
| 0111 | 7 | | 1111 | −7 |

Evaluating against our criteria:
- **Wasted values?** Yes — **two ways to write 0** (`0000` and `1000`). ✗
- **Easy to negate?** Yes — just flip the first bit. ✓
- **Same addition circuitry?** No: `1 + (−1) = 0001 + 1001 = 1010 = −2`. ✗

Sign-magnitude fails two of three criteria.

## The Winner: Two's Complement (2C)

Idea: the **first bit's place value is *negative*** its normal value (e.g., −8 in 4 bits). All other places are positive as usual.

| Code | 2C value | | Code | 2C value |
|------|----------|---|------|----------|
| 0000 | 0 | | 1000 | **−8** |
| 0001 | 1 | | 1001 | −7 |
| 0010 | 2 | | 1010 | −6 |
| 0111 | 7 | | 1111 | **−1** |

4-bit place values are: **−8, 4, 2, 1**.

| Bits | Computation | Value |
|------|-------------|-------|
| `1101` | −8 + 4 + 1 | **−3** |
| `1001` | −8 + 1 | **−7** |

Evaluating against our criteria:
- **Wasted values?** None. ✓
- **Same addition circuitry?** Yes: `1 + (−1) = 0001 + 1111 = 0000 = 0`. ✓
- **Easy to negate?** There's a trick (below). ✓

**This is why real computers use two's complement.**

### Recognizing key values in 2C
- **Smallest:** first bit 1, all others 0 (e.g., `1000` = −8).
- **Largest:** first bit 0, all others 1 (e.g., `0111` = 7).
- **−1:** all bits 1 (`1111`).

| Type | Range |
|------|-------|
| `char` | [−128, 127] |
| `short` | [−32,768, 32,767] |
| `int` | [−2,147,483,648, 2,147,483,647] |
| `long` | [−9,223,372,036,854,775,808, 9,223,372,036,854,775,807] |

## Negating a Two's-Complement Number

> **The rule: flip all the bits and add 1.**

**Example — negate 5:**
```
 5:  0101
flip: 1010
 +1:  1011   →  −8 + 2 + 1 = −5  ✓
```

**Example — negate −1:**
```
−1:  1111
flip: 0000
 +1:  0001   →  1  ✓
```

### The one weird case: −(−8)
```
−8:  1000
flip: 0111
 +1:  1000   →  −8  again!
```
**−8 is its own negative.** Why? We *cannot represent +8* in 4 bits, so this is an **overflow error**. The same happens for the **minimum value of every type size** (e.g., `INT_MIN`). In fact, `−2147483647 − 1 = TMin` for 32-bit ints.

## Conversions Between Signed and Unsigned (same size)

> **Key idea: the bits stay the same. They are just *interpreted* differently.**

```c
unsigned char u = 143;   // bits: 1000 1111
char s = (char) u;       // same bits, read as 2C → s == −113
```

### Mixing signed and unsigned in one expression
If you mix signed and unsigned of the same size, **the signed value is implicitly cast to unsigned.** This is a **major source of weird, non-intuitive bugs** — be careful with comparisons like `signed < unsigned`.

## Conversions Between Sizes

### Truncating (to fewer bits): `int → short`, `int → char`
- Only the **low-order bits** are kept; they stay the same.
- Higher-order bits are **lost**.
- Example: 4-bit `1011` truncated to 3 bits → `011`.

### Expanding (to more bits): `short → int`, `char → int`
- Low-order bits stay the same; **high-order bits get filled in**:
  - **Unsigned:** fill with **0** (zero-extend).
  - **Signed:** **sign-extend** — fill every new bit with the **most significant (sign) bit**.

Examples:
- `011` → `0011` (same for signed and unsigned)
- `110` → `0110` (unsigned) but `1110` (signed)

### Double conversion (size *and* signed-ness change)
The size change happens first, based on the **original** type's signed-ness:

```c
short sx = 0xcfc7;      // signed
unsigned uy = sx;
printf("uy = %x", uy);  // prints 0xffffcfc7
```
Because `sx` was **signed**, `cfc7` is **sign-extended** to 32 bits, and *then* those bits are interpreted as unsigned.

## Common Pitfalls & Misconceptions

- **Thinking the sign bit just "means negative."** In two's complement the top bit isn't a flag — it carries a **negative place value** (e.g., −8 in 4 bits). That's why `1111` is −1, not −7. (Sign-magnitude is the scheme where the top bit is a flag, and we rejected it.)
- **The `signed < unsigned` trap.** `if (x < sizeof(arr))` where `x` is a negative `int` can be **false**, because `sizeof` is unsigned, so `x` is converted to a huge unsigned value. This causes real, hard-to-find bugs. Be deliberate about signedness in comparisons.
- **Expecting symmetric ranges.** A signed type reaches one *more* negative value than positive (`int`: −2,147,483,648 … +2,147,483,647). Hence `-INT_MIN` overflows back to `INT_MIN`.
- **Confusing truncation with extension.** Going to *fewer* bits keeps the **low** bits (truncation). Going to *more* bits fills the **high** bits — with **0** for unsigned, with the **sign bit** for signed.
- **Forgetting the order in double conversions.** When both size and signedness change, the **size change happens first**, using the *source's* signedness. That's why a negative `short` widened to `unsigned` shows `0xffff…`.

## Summary

| Concept | Rule |
|---------|------|
| Unsigned | Normal binary |
| Signed | **Two's complement** |
| Negation | Flip all bits, add 1 |
| Signed ↔ unsigned (same size) | **Bits stay the same**, reinterpreted |
| Truncation | Keep low bits, drop high bits |
| Expansion (unsigned) | Zero-extend (fill 0) |
| Expansion (signed) | **Sign-extend** (fill with sign bit) |
| Mixing signed & unsigned | **Signed is converted to unsigned** — be careful! |

## Self-Check

1. With 4 bits, why does adding negatives cost us some positive values?
2. List the three criteria for a good signed representation, and say how two's complement does on each.
3. Negate the 4-bit value `0110` using flip-and-add-1. Verify your answer.
4. Why is the most negative value of any type "its own negative"?
5. `char c = (char) 200u;` — what value does `c` hold, and why didn't the bits change?
6. Sign-extend the 4-bit signed value `1010` to 8 bits. Zero-extend the 4-bit unsigned value `1010` to 8 bits.