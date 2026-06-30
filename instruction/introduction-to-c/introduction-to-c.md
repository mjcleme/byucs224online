# Introduction to C

## Learning Objectives

After this lesson you should be able to:
- Explain why C is the language of choice for **systems programming** and how it relates to C++.
- Pick an appropriate C **type** (`char`/`short`/`int`/`long`, `float`/`double`) given a value's range or purpose.
- Write **control flow** in C: `if/else`, `while`, `for`, and `switch`.
- Use **`printf`/`scanf`** with the correct **format specifiers**, and explain why `scanf` needs the **address-of (`&`)** operator.

> **If you know C++ already:** focus on the *differences* — C has no classes, no `cin`/`cout`, no `string` type (strings are `char` arrays), no `bool` before `<stdbool.h>`, and no automatic bounds checking. Everything is closer to the memory.

## Background: Why C?

- C came after the language **B** (and its predecessor **BCPL**), which influenced it.
- C has been called *"quirky, flawed, and an enormous success."*
- C is **closely tied to the Unix operating system** — it was designed to *implement* Unix.
- It is a **small, simple language**, with its design controlled by a single person (Dennis Ritchie).
- It is a **"low-level" language** — the language of choice for **system-level programming**, replacing the use of raw assembly for those tasks.

> *"Since C is relatively small, it can be described in a small space, and learned quickly. A programmer can reasonably expect to know and understand and indeed regularly use the entire language."*
> — *The C Programming Language*, Kernighan & Ritchie

### C in Relation to C++

C is essentially a subset that sits **inside** C++. If you know C++, much of C will look familiar — C is the smaller, lower-level core.

## C Data Types

### Whole-number (integer) types
These differ in how much **memory (space)** they use, which determines how many values they can hold:

| Type | Role |
|------|------|
| `char` | Smallest — holds small numbers (256 different values) **or** a single character (`'A'`, `'e'`, `';'`) |
| `short` | Next biggest — short integer (fewer possible values) |
| `int` | Normal-size integer (whole numbers) |
| `long` | Long integer — more possible values (bigger size = more numbers) |

### Floating-point types
Numbers with fractional parts (e.g., `3.14159`):

| Type | Role |
|------|------|
| `float` | Normal-size floating point |
| `double` | High-precision floating point (more decimal places) |

**Big idea:** bigger type → more memory → more possible values.

## Control Flow

C control flow is **the same as C++** and similar to Python.

### if / else
```c
if (expression) {
    statement
}
else {
    statement
}
```

### Loops
```c
while (expression) {
    statement
}

for (expr1; expr2; expr3) {
    statement
}
```

### switch
```c
switch (expression) {
    case const-expr:
        statements
    case const-expr:
        statements
    default:
        statements
}
```

## Input and Output

### Output — STDOUT
On the command line, `cat` and `echo` write to STDOUT. In code:

| | Print text | Print a variable |
|--|-----------|------------------|
| Python | `print("Hello")` | `print("This is num: ", num)` |
| C | `printf("Hello");` | `printf("This is num: %d\n", num);` |

C uses **format strings** with placeholders:

| Specifier | Meaning |
|-----------|---------|
| `%c` | character |
| `%d` | decimal integer |
| `%x` | hexadecimal integer |
| `%f` | floating-point number |
| `%l` | double |
| `%s` | character string (array of chars) |

There are many more options (significant digits, decimal places, etc.).

### Input — STDIN

| | Read input |
|--|-----------|
| Python | `num = input()` |
| C | `scanf("%d", &num);` |

`scanf` uses the **same format specifiers** as `printf`. Note the `&` before the variable — you must pass the **address** of the variable so `scanf` knows *where* to store what it reads. (More on this in the memory unit.)

## Try It

You can compile and run C in the browser:
`https://www.tutorialspoint.com/compile_c_online.php`

## Common Pitfalls & Misconceptions

- **Forgetting `&` in `scanf`.** `scanf("%d", num)` (without `&`) compiles but corrupts memory at runtime, because `scanf` needs the variable's **address** to know where to write. Use `scanf("%d", &num)`.
- **`=` vs `==`.** `if (x = 5)` *assigns* 5 to `x` and is always true. You almost always want `if (x == 5)`.
- **Mismatched format specifier and type.** `printf("%d", 3.14)` is undefined behavior — `%d` expects an `int`, not a `double`. Match the specifier to the argument's type (`%f` for `double`/`float` in `printf`).
- **Forgetting `\n`.** C does not add a newline for you the way Python's `print` does; output may look "stuck together" or buffered until the program ends.
- **Assuming sizes are fixed.** `int` is *usually* 4 bytes and `long` 8, but the standard only guarantees minimums. When exact width matters, use `int32_t`/`int64_t` (covered in the memory unit).

## Key Takeaways

- C is small, low-level, and tied to Unix/system programming; it sits inside C++.
- Integer types (`char`, `short`, `int`, `long`) trade memory for range; floats (`float`, `double`) hold fractional values.
- Control flow mirrors C++: `if/else`, `while`, `for`, `switch`.
- `printf`/`scanf` use **format specifiers** (`%d`, `%c`, `%x`, `%f`, `%s`); `scanf` needs the **address** of the target (`&num`).

## Self-Check

1. Why does `char` only hold 256 different values?
2. What is the difference between `%d` and `%x` in a format string?
3. Why does `scanf("%d", &num)` use `&` but `printf("%d", num)` does not?
4. Write a `for` loop in C that prints the numbers 0 through 9.