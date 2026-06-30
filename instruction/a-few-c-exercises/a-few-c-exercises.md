# A Few C Exercises

## Learning Objectives

After working these exercises you should be able to:
- Read input and produce output in C using `scanf`/`printf` without looking up the syntax.
- Implement the **read → accumulate-in-a-loop → print** pattern correctly.
- Choose correct **loop bounds** (`<` vs `<=`) and **accumulator initial values** (0 for sums, 1 for products).
- Add simple **branching** so one program can perform different operations based on user choice.

---

This is a hands-on practice session. Use the online compiler to write, run, and debug each program:
`https://www.tutorialspoint.com/compile_c_online.php`

These exercises reinforce the basics from the **Intro to C** material: reading input with `scanf`, printing with `printf`, loops, and simple branching.

## Exercise 0 — Sum Below a Number

> Get an integer from the user and output the sum of all the integers **less than** that number.

**Hints**
- Read the number with `scanf("%d", &n);`
- Accumulate a running total in a loop: `for (int i = 0; i < n; i++) sum += i;`
- Print the result with `printf`.

<details>
<summary>One possible solution</summary>

```c
#include <stdio.h>
int main() {
    int n, sum = 0;
    scanf("%d", &n);
    for (int i = 0; i < n; i++) {
        sum += i;
    }
    printf("Sum = %d\n", sum);
    return 0;
}
```
</details>

## Exercise 1 — Multiply Using Addition

> Get **two positive integers** from the user and multiply them using **addition and a `for` loop**, then display the product.

**Hints**
- Read both numbers with `scanf`.
- Add the first number to a running total, the second number of times.

<details>
<summary>One possible solution</summary>

```c
#include <stdio.h>
int main() {
    int a, b, product = 0;
    scanf("%d %d", &a, &b);
    for (int i = 0; i < b; i++) {
        product += a;
    }
    printf("Product = %d\n", product);
    return 0;
}
```
</details>

## Exercise 2 — Sum or Product (User's Choice)

> Ask the user for a number `n` and let them **choose** between computing the **sum** or the **product** of `1, …, n`.

**Hints**
- Read `n` and a choice (e.g., `1` for sum, `2` for product).
- Use `if`/`else` or `switch` to pick the operation.
- Remember: a product should start its accumulator at **1**, not 0.

<details>
<summary>One possible solution</summary>

```c
#include <stdio.h>
int main() {
    int n, choice;
    scanf("%d", &n);
    printf("1 = sum, 2 = product: ");
    scanf("%d", &choice);

    if (choice == 1) {
        int sum = 0;
        for (int i = 1; i <= n; i++) sum += i;
        printf("Sum = %d\n", sum);
    } else {
        int product = 1;
        for (int i = 1; i <= n; i++) product *= i;
        printf("Product = %d\n", product);
    }
    return 0;
}
```
</details>

## Common Pitfalls & Misconceptions

- **Off-by-one errors.** "All integers *less than* n" means `i < n`; "1 through n" means `i <= n`. Decide which you want *before* writing the loop.
- **Wrong accumulator start.** A sum that starts at 1, or a product that starts at 0, silently gives wrong answers. Sums start at **0**; products start at **1**.
- **Uninitialized variables.** In C, a local variable like `int sum;` does **not** start at 0 — it holds garbage. Always initialize (`int sum = 0;`).
- **Integer division surprises.** `5 / 2` is `2` in C, not `2.5`, because both operands are `int`. This is expected here, but be aware of it.

## Key Takeaways

- Practice the read → loop → print pattern until it's automatic.
- Watch your **accumulator's starting value**: 0 for sums, 1 for products.
- Watch your **loop bounds**: "less than `n`" (`i < n`) vs. "up to and including `n`" (`i <= n`).

## Self-Check

1. In Exercise 0, why does the loop use `i < n` instead of `i <= n`?
2. Why must the product accumulator in Exercise 2 start at 1?
3. How would you change Exercise 1 to validate that the inputs are positive?