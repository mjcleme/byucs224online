## What This Course Is About

CS 224 answers one big question: **How does a computer *really* work?**

More specifically:
- What happens when I run a program?
- How does a computer **represent** information (images, files, programs)?
- How does a computer **manipulate** that information?

CS 111 taught you to *write* programs. CS 224 takes you underneath the code to see how those programs actually run on hardware.

## The Central Idea: Abstraction

> An **abstraction** is a model of something that **ignores irrelevant details** while **preserving important details**.

A single system can have many abstractions at once:
- **High-level abstraction** → fewer details preserved (simpler, easier to reason about)
- **Low-level abstraction** → more details preserved (more accurate, more complex)

### Example: A Vehicle

You can describe a car at increasing levels of detail:

| Level | Description |
|-------|-------------|
| 1 | It transports things. |
| 2 | It transports things on the ground and uses energy. |
| 3 | …uses energy from gasoline; controlled by a steering wheel and pedals. |
| 4 | …controlled by a steering wheel, gas, and brake pedals. |
| 5 | …the engine makes the car go, influenced by the gas pedal. |
| 6 | …gas is combusted by spark plugs, which move pistons, etc. |

The right level of detail depends on **who you are**: a child, a typical adult, Google Maps, a bus driver, a racecar driver, a mechanic, a car designer, an engine designer — each needs a different level.

### The Same Idea for Computers

From high level to low level, a computer can be understood as:

```
Computer
  Applications
    Algorithms
      Higher-Level Languages
        C Code
          Assembly Code
            Machine Code
              Circuits
                Gates
                  Transistors
                    Electrons / Quantum Mechanics
```

- **CS 111** focused near the top (applications, algorithms, high-level languages).
- **CS 224** lives in the middle layers: **C code → assembly → machine code → circuits/gates**.

Different roles need different levels: a person with a phone, an accountant, a scientist, a programmer, a system administrator, and a computer designer all need different depths of understanding.

## The Core Computer Abstraction

> **Computer = Memory + Processing**

- **Memory** — storage of information as 0s and 1s.
- **Processing (CPU)** — manipulating that information.

This simple model (a big array of bits plus a processor that reads and changes them) is the lens for the whole course.

## Course Roadmap

| Units | Topic |
|-------|-------|
| 1 | Linux command line |
| 2 | C language basics |
| 3 | Representing information: connecting C to memory (Ch. 2) |
| 4–7 | Manipulating information: low-level programs and their execution — assembly (Ch. 3–4) |
| 8–11 | Putting it all together: Bomb Project & Attack Project |
| 12–14 | Caches & Performance (Ch. 6) |

## Course Logistics (Highlights)

- **Weekly Lab Assignments — 30%**
  - Lab prep quiz (5%)
  - Lab assignment (10%)
  - Unfinished lab work becomes homework
- **Projects — 40%**
  - 5 projects across the semester
  - **Must be completed on CS lab machines**
- **Exams — 30%**: 2 midterms + a final
- **Late policy**: up to −10% per weekday
- **Labs** are TA-led: work on the assignment, ask questions, help each other. Attendance is recorded.
- **Getting help**: use Discord (`#ta-help-queue`) — it creates a record that helps other students too. TA help can be virtual or in person.

## How to Succeed

- **Put in the time.** Expect ~18 hours/week (3 in class, 1 in lab, ~5 outside, twice per week).
- Attend and **participate** in class and lab.
- Get to know your classmates; do the reading.
- **Details matter** in this class — aim to "be the expert."
- **Ask questions.** If you're confused, many others are too.

### Tips for Projects

- **Start the day we cover it in class** — usually well before the due date.
- Read the project handout *and* the starter code carefully **before** lab.
- It's fine to use Google for C syntax, **but make sure you understand the solution.** If the solution doesn't look familiar, it's probably not how we meant you to do it.
- Your code should be **short** — under ~10 lines per function we give you. We provide good structure; follow it.
- **Don't optimize first.** Do it the simple way, then optimize only if needed.
- When stuck, ask: TA, classmates, professor, Discord. **Turn in something** — don't get behind.

## Common Pitfalls & Misconceptions

- **"Lower level is always better."** No — the goal is the *right* level for the question. A web developer rarely needs transistor physics; a compiler writer does. CS 224 deliberately works at the **middle** levels because that's where "what the machine actually does" becomes visible.
- **"This is just a programming class."** It isn't. Much of the grade is understanding *how and why* code behaves the way it does on real hardware. Reading and reasoning matter as much as writing code.
- **Starting projects late.** Projects build on the lecture they're assigned with and take real time. The single most common cause of a low grade is starting the night before.
- **Treating labs as optional.** Lab attendance is recorded and lab time is where the TA can unblock you fastest.

## Key Takeaways

- CS 224 is about understanding computers at the **C / assembly / machine / circuit** levels.
- **Abstraction** is the organizing principle: pick the level of detail that fits the question.
- The whole course rests on one model: **Computer = Memory + Processing.**
- Success comes from steady time, participation, and asking questions early.

## Self-Check

1. In your own words, what is an abstraction? Give a high-level and a low-level description of something familiar.
2. Where did CS 111 sit in the abstraction stack, and where does CS 224 sit?
3. What are the two halves of the basic computer abstraction, and what does each do?
4. Why is starting projects early specifically emphasized for this course?