# Introduction to Memory and C

## Learning Objectives

After this lesson you should be able to:
- Describe memory as a **byte-addressable array** and state how many bytes each C type occupies.
- Explain that **bits have no meaning without context (a type)**, and give an example.
- Define a **pointer** as an address, and use `&` (address-of) and `*` (dereference) correctly.
- Draw the **memory layout** of a variable, an array, and a C-string.
- State and apply the **little-endian** byte-ordering rule for multi-byte values.
- Explain what **typecasting a pointer** does (and does not do) to memory.

> **Why this lesson is the hinge of the course:** from here on, "understanding a program" means being able to **draw what is in memory** at each step. Practice the memory diagrams until they're automatic — every later unit (assembly, the Bomb, the Attack Lab, caches) depends on this skill.

## The Model: Computer = Memory + Processing

- **Memory** stores information as **0s and 1s**.
- **Processing (CPU)** manipulates that information.

Everything in this unit is about understanding **memory** — what's stored, where, and how to interpret it.

## Bits and Bytes

- A **bit** is a single 0 or 1.
- A **byte** is the basic unit of memory = **8 bits**.
- Memory is **byte-addressable**: every byte has its own **address** (0, 1, 2, 3, …).
- So **memory = an array of bytes**.

## Interpreting Memory: Context Matters

The bits in memory have **no inherent meaning** — meaning comes from **context**.

> What does `011921` mean?

It depends:
- On a car: an **odometer** reading (`ODO 011921`).
- On a carton: a **"best by" date** (Jan 19, 2021).

The same bytes can be an integer, a character, part of an address, or part of a date. **The type tells the computer how to interpret the bytes.**

## Bytes and Types

Each C type uses a **specific number of bytes**:

| Type | Bytes |
|------|-------|
| `char` | 1 |
| `short` | 2 |
| `int` | 4 |
| `long` | 4 or 8 |
| `float` | 4 |
| `double` | 8 |
| `int32_t` | 4 |
| `int64_t` | 8 |
| `char *` (pointer) | 4 or 8 |

A **pointer** type stores a **memory address**.

### Aside: 32-bit vs. 64-bit

A processor's **word size** is usually the size of an address. It limits how much memory can be addressed (and how many bits the CPU processes at once):

- **32-bit** → 2³² addresses = 4,294,967,296 bytes (~4 GB)
- **64-bit** → 2⁶⁴ addresses = 1.8446744 × 10¹⁹ bytes

(For fun: Atari 2600 and NES were 8-bit, SNES 16-bit, Xbox 32-bit, Nintendo 64 was 64-bit.)

## Hexadecimal Notation

Memory contents are usually shown in **hex**:

- 16 symbols: `0 1 2 3 4 5 6 7 8 9 A B C D E F`
- **1 hex digit = 4 bits**, so **2 hex digits = 1 byte**.
- Hex numbers usually have a leading `0x` (e.g., `0x1234ab`).
- In C: read hex with `scanf("%x", &a)`, print hex with `printf("%x", a)`.

## Our Memory Picture

In this course we draw memory as a grid:
- **Four bytes to a line.**
- **Addresses increase as we move up.**
- The address shown for a line is the address of the **right-most byte**.
- Addresses are written in **hex**.

## Variables, Addresses, and Pointers

Given `char a;` — `a` is a variable of type `char`, taking **1 byte** in memory.

| Expression | Meaning |
|-----------|---------|
| `a` | the value stored |
| `&a` | the **address** of `a` (where in memory its byte lives); its type is `char *` |
| `char *a_p = &a;` | `a_p` is a pointer holding the address of `a` |
| `*a_p` | **dereference** — take the address and return the variable it points at |

This explains `scanf("%c", &a);`:
- We pass the **address of `a`** so `scanf` knows *where* to start storing input.
- The format specifier (`%c`) tells `scanf` what **format** the input is in.

## Arrays

An **array** is space set aside for a given number of elements of one type, stored in **consecutive bytes**:

| Declaration | Size |
|-------------|------|
| `char a[5]` | 5 × 1 = 5 bytes |
| `int b[5]` | 5 × 4 = 20 bytes |
| `double c[5]` | 5 × 8 = 40 bytes |

> **Key idea:** Arrays are stored **consecutively** in memory — the next element is at the next-highest address.

## C-Strings

A **C-string** is an **array of `char`** with one extra rule: the final used spot holds a **null character** — a byte of all zeros (`00`), written `'\0'`.

```c
char a[] = "Cougars";
```

In memory (one byte per character, ASCII), this is:

| C | o | u | g | a | r | s | `00` |
|---|---|---|---|---|---|---|------|
| 43 | 6f | 75 | 67 | 61 | 72 | 73 | 00 |

(The hex values are the ASCII codes; `00` is the null terminator.)

> **Key idea: C does NOT track array sizes.**
> ```c
> char a[] = "BYU";   // uses bytes for 'B','Y','U','\0'
> a[5] = 'X';         // writes PAST the array — into whatever is next!
> ```
> C will happily let you write out of bounds, corrupting neighboring memory. This is the root of many bugs and security exploits later in the course.

## Local Variables in Memory

> **Key idea:** Local variables are stored **next to each other** (though not always touching).

```c
int a = 224;
char b[] = "BYU";
```
Usually the **first declared** variable is stored at the **highest** memory address.

## Multi-byte Types and Byte Ordering

For any type bigger than a `char`, we must decide **what order the bytes go in**.

- The **address of the variable** is the **lowest address** it uses.
- This machine is **little-endian**: the **least significant byte is stored in the lowest address**, and the most significant byte in the highest address.

Example: `int a = 0x11223344` (the `44` is least significant):

| Address | 0 | 1 | 2 | 3 |
|---------|---|---|---|---|
| Byte | `44` | `33` | `22` | `11` |

Example: `int a = 0x12345678`, in a 4-bytes-per-line picture, bytes go `78 56 34 12` from low address to high.

## Typecasting

> **Key idea:** Typecasting a pointer **changes how the memory is interpreted**, not the memory itself.

```c
int i = 0xaabbccdd;
int *ip = &i;
char *cp = (char *)ip;   // same address, now read one byte at a time
```

- The **address stays the same**.
- Only the compiler's **interpretation** changes.
- The **contents of memory do not change.**

Another example — reading characters as an integer:
```c
char a[] = "abcdefg";
int *i = (int *)&a;   // reinterpret the first 4 chars as one int
```

## Common Pitfalls & Misconceptions

- **Confusing a value with its address.** `a` is the value; `&a` is *where it lives*; `*p` is the value *at* the address `p`. Mixing these up is the #1 pointer error. Read `*` as "the thing pointed to by."
- **Thinking little-endian "reverses the number."** It doesn't reverse the *value* — only the order the **bytes** sit in memory. `0x11223344` is still the number `0x11223344`; the byte `0x44` just happens to live at the lowest address. You only notice the ordering when you inspect memory byte-by-byte (e.g., through a `char *`).
- **Believing a cast changes the bytes.** `(char *)ip` changes only how the compiler *interprets* the address — the bits in memory are untouched. A cast is a new pair of glasses, not a new value.
- **Forgetting the null terminator.** `char s[] = "BYU"` uses **4** bytes, not 3, because of the trailing `'\0'`. Sizing a string buffer as exactly the visible characters is a classic bug.
- **Assuming C bounds-checks arrays.** It does not. `a[5]` on a 3-element array writes into neighboring memory with no warning — this is the seed of the buffer-overflow attacks studied later.

## Summary of Key Ideas

- Memory is an **array of bytes**, each with an **address**.
- Each **type** uses a specific number of bytes.
- **Pointers are addresses** (and are themselves stored in memory).
- **Arrays** are stored consecutively, low → high addresses.
- **C-strings** are arrays of `char` ending in a null byte.
- **C does not track array sizes** — out-of-bounds writes are allowed and dangerous.
- **Local variables** are stored next to each other (first declared usually at the highest address).
- For **multi-byte types**: the address is the lowest byte, and the **least significant byte lives at the lowest address** (little-endian).
- **Typecasting** changes interpretation, not the underlying bytes.

## Self-Check

1. The bytes `011921` could mean several things. What determines their meaning?
2. How many bytes does `int b[10]` occupy? How many does `double c[10]`?
3. Write the in-memory byte layout (low → high address) for `int x = 0xDEADBEEF`.
4. What goes wrong with `char s[] = "Hi"; s[10] = '!';`?
5. After `char *cp = (char *)&someInt;`, what does `*cp` give you, and did the int's value change?