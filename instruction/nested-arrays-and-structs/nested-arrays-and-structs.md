
# X86-64: Arrays, Nested Arrays & Structs

## Learning Objectives

After this lesson you should be able to:
- Compute the **size** and the **address of any element** of a 1-D or 2-D array given the element type.
- Explain **pointer arithmetic scaling** (`p + i` advances by `i × sizeof(T)`) and why `A[i] == *(A+i)`.
- Apply the **row-major** address formula `&D[i][j] = x + L·C·i + L·j`.
- Lay out a **struct** with correct **offsets**, and apply the **alignment** rule (a K-byte object needs a K-aligned address).
- Predict how **field ordering** changes a struct's padding and total size.

---

This material connects C **arrays and structs** to their **memory layout** and the **address arithmetic** the compiler generates.

## Arrays in Memory

For a declaration `T A[N]`, let:
- `x` = the **starting address** of array `A`,
- `L` = the **size in bytes** of type `T`.

Then:
- It **allocates `N × L` bytes** of memory.
- `A` is a **pointer to the beginning** of the array (`A = x`).

## Pointer Arithmetic

If `p` is a pointer to type `T` (size `L`) and `p = x`:

> **`p + i = x + L·i`** — pointer arithmetic is automatically **scaled by the type size.**

Key identities:
- **`&`** generates a pointer: `&E` is the address of object `E`.
- **`*`** dereferences: if `AE` is an address, `*AE` is the value there.
- `E = *&E`
- **`A[i] = *(A + i)`** — array indexing *is* pointer arithmetic plus a dereference.

So `A[i]` lives at address `x + L·i`. (In assembly this is exactly the `D(rb, ri, s)` addressing mode, with `s = L`.)

## Nested Arrays (Arrays of Arrays)

Example: `int A[5][3]` — **5 rows**, each a row of **3 columns**.
- The **base object is a row**: an array of 3 ints.
- **Size of one row** = `L · C` (element size × columns).
- **Size of the whole array** = `L · C · R`.
- Stored in **row-major order** (row 0, then row 1, …).

### General address formula
For `T D[R][C]`:

> **`&D[i][j] = x + L·(C·i + j) = x + L·C·i + L·j`**

- `x + L·C·i` = start of the **i-th row**,
- `+ L·j` = the **j-th element** within that row.

### Worked examples

**`char C[3][4]`** (element size 1):
- Element = 1 byte; row = `1 × 4 = 4` bytes; total = `3 × 4 = 12` bytes.
- `&C[i][j] = C + 4*i + j`.

**`short S[5][3]`** (element size 2):
- Element = 2 bytes; row = `2 × 3 = 6` bytes; total = `5 × 6 = 30` bytes.
- `&S[i][j] = S + 6*i + 2*j`.

**`int A[2][4]`** (element size 4):
- Element = 4 bytes; row = `4 × 4 = 16` bytes; total = `2 × 16 = 32` bytes.
- `&A[i][j] = A + 16*i + 4*j`.

## Structs

A **struct** groups objects of **different types** into one object, **arranged in order** in memory.

```c
struct rec {
    int i;       // offset 0
    int j;       // offset 4
    int a[2];    // offset 8  (a[0] at 8, a[1] at 12)
    int *p;      // offset 16
};
```
Each field is accessed by its **fixed offset** from the start of the struct. The compiler knows these offsets at compile time.

## Data Alignment

> **Alignment rule:** an object of `K` bytes must have an address that is a **multiple of `K`.**

To satisfy this, the compiler inserts **gaps (padding)** into structs.

| K | Types |
|---|-------|
| 1 | `char` |
| 2 | `short` |
| 4 | `int`, `float` |
| 8 | `long`, `double`, `char *` (pointers) |

### Example: field order matters

```c
struct S1 {       // offsets:  i=0, c=4, j=8   → with a GAP between c and j
    int i;
    char c;
    int j;
};
```
`c` only needs 1 byte at offset 4, but `j` (an `int`) must start at a multiple of 4 → the bytes at offsets 5–7 are **padding**.

```c
struct S2 {       // offsets:  i=0, j=4, c=8   → GAP after c
    int i;
    int j;
    char c;
};               // total size: 12 bytes
```
`S2` is padded out to **12 bytes** (a multiple of 4) so that **an array of `S2`** keeps every element properly aligned.

> **Takeaway:** the **same fields in a different order** can produce a different total size. Ordering fields largest-to-smallest often minimizes padding.

## Common Pitfalls & Misconceptions

- **Forgetting pointer arithmetic is scaled.** `p + 1` on an `int *` advances **4 bytes**, not 1. In assembly you'll see this as the **scale factor** `s` in `D(rb, ri, s)`. Mixing up byte offsets and element offsets is a top source of array bugs.
- **Mixing up rows and columns in the 2-D formula.** For `D[R][C]`, you scale the **row index by C** (the number of *columns*), not by R: `x + L·C·i + L·j`. Using R gives wrong addresses for non-square arrays.
- **Assuming a struct's size is the sum of its fields.** **Padding** inserted for alignment usually makes it larger, and the struct is padded so its **total size** is a multiple of its largest member's alignment (so arrays of it stay aligned).
- **Thinking field order doesn't matter.** `{int; char; int;}` and `{int; int; char;}` can have different sizes. Ordering members **largest-to-smallest** generally minimizes padding.
- **Treating a 2-D array as an array of pointers.** `int A[5][3]` is **one contiguous block** (row-major), not five separate row pointers. `int *A[5]` *is* an array of pointers — a different layout entirely.

## Key Takeaways

- An array `T A[N]` is `N·L` contiguous bytes; `A[i]` is at `x + L·i` (`A[i] == *(A+i)`).
- Pointer arithmetic is **scaled by the element size**.
- 2D arrays are **row-major**; `&D[i][j] = x + L·C·i + L·j`.
- Structs lay fields out **in order** at fixed **offsets**.
- **Alignment** (K-byte object at a multiple of K) forces **padding**; field order affects struct size.

```masteryls
{"id":"6886b2ac-2bff-4411-9d70-4a44f79b932a", "title":"Takeaways", "type":"essay", "gradingCriteria":"- Addresses the prompt directly\n- Uses at least one concrete example\n- Demonstrates accurate understanding of key concepts" }
For "double d[10]" describe how many bytes are allocated and what the address of d[3] is
```

```masteryls
{"id":"8604f84a-74f6-49b8-90d2-a286069da7ff", "title":"Teaching", "type":"teaching" }
teach me how to find the address of M[i][j] in intM[4][6].
```

## Self-Check

1. For `double d[10]`, how many bytes are allocated, and what is the address of `d[3]` relative to `d`?
2. Give the address formula for `int M[4][6]` element `M[i][j]`.
3. Lay out `struct { char a; int b; char c; }` with offsets and total size, marking any padding.
4. Reorder the fields of that struct to minimize its size. What's the new size?
5. Why does pointer arithmetic `p + 1` advance by more than one byte for `int *p`?
