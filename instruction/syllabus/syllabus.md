# CS 224 — Computer Systems — Syllabus

## Instructors

| Section | Instructor | Email | Office |
|---------|-----------|-------|--------|
| 1 | Mark Clement | clement@cs.byu.edu | 3374 TMCB |


The TA help, the help queue, and lab sessions are coordinated through Canvas and the class Discord (`#ta-help-queue`).

## What Is CS 224?

You've probably heard that to a computer, everything is 0s and 1s. *What does that mean? How does a computer really work?* This course explores two high-level questions:

1. **How is information represented and stored** on a computer system?
2. **How is information manipulated** by the computer?

Along the way you'll learn the **C programming language**, the **command line**, a **debugger**, and gain an understanding of what happens "under the hood" — knowledge that makes you a stronger programmer and problem solver.

### Real-world motivation
Low-level hardware behavior affects the **correctness, performance, security, and utility** of programs (e.g., the Meltdown vulnerability in two decades of Intel processors). The course asks: How can a programmer use hardware abstractions to avoid defects? How does the relationship between your code and the underlying architecture affect performance and security? Why is the *memory mountain* critical to modern computing, and how do you use it to write faster code?

## Course Purpose & Learning Outcomes

**Purpose:** Upon completion, the conscientious student will be able to interact with a computer system via the command line and the C language, understand how a computer represents and manipulates information and instructions at the lowest level, and leverage those skills to debug and write software more effectively.

By the end of the semester you will be able to:

- **Work fluently in a command-line environment** — compile and debug C programs with command-line tools; create, start, stop, and kill processes.
- **Use C and the memory model** — pass by reference with pointers; manipulate arrays/elements via pointers.
- **Represent & manipulate information at the machine level** — convert base-10 to unsigned and two's-complement; show where representations break; map structs/arrays to the memory model; carry out integer arithmetic.
- **Represent programs at the machine level** — read machine code; use the runtime stack for local storage, control transfer, and data transfer; lay out arrays and heterogeneous data structures in memory.
- **Explain processor architecture mechanics** — assemble machine code to binary; decode and move instructions through a sequential micro-architecture.
- **Identify & eliminate performance bottlenecks** — avoid optimization blockers; exploit temporal and spatial locality; identify common memory bugs; reason about cache hit/miss behavior.

| Ability | Assessed by |
|---------|-------------|
| Work fluently in a command-line environment | Labs 1–5, Projects 1–5, Exams |
| Represent & manipulate information at machine level | Labs 1–4, Projects 1–4, Exams |
| Represent programs at machine level | Labs 3–4, Projects 3–5, Exams |
| Explain processor architecture mechanics | Labs 3–4, Projects 3–5, Exams |
| Identify & eliminate performance bottlenecks | Lab 5, Exams |

## Prerequisites & Textbook

- **Prerequisite:** Pass **CS 235** before taking CS 224.
- **Textbook:** *Computer Systems: A Programmer's Perspective*, 3rd ed. (**CS:APP3e**), ISBN-13 **978-0134092669**. The course focuses on **Chapters 2, 3, 4, and 6**.

## How the Course Runs

A regular weekly rhythm:
1. **Read** the assigned background material before lecture.
2. **Complete the lab pre-quiz** before coming to lab.
3. **Lecture** reviews the material and works example problems.
4. **Lab** gives hands-on practice on the most important topics and projects.

Each week introduces new material, so **keep up with the schedule** and start labs/projects as soon as you've learned the material — that's when help from class and TAs is most available. See [SCHEDULE.md](SCHEDULE.md).

## Grading

| Category | Weight |
|----------|--------|
| Projects (5) | **40%** |
| Labs (incl. lab-prep quizzes) | **30%** |
| Exams (2 midterms + final) | **30%** |

*In Canvas the exam weight is split as Midterms **20%** + Final **10%**.* Full point values and due dates are in [ASSIGNMENTS.md](ASSIGNMENTS.md).

### Late policy
- Labs and Projects may be submitted late at **−10% per day**, to a **maximum −40%**.
- **Late days do not accrue on Sundays** — use the Sabbath as a day of rest.
- **After two weeks past the due date, late work is not accepted** (these are the "close" dates in [ASSIGNMENTS.md](ASSIGNMENTS.md)).
- **All work must be submitted by the last day of classes** — no extension allows submission after that.

### Project style guide
For the first three C projects (myxxd, Bitmap Filter, Y86-64 simulator), TAs grade **code style** worth **15 of 200 points** (−5 per style problem, max −15). Common deductions:
- A non-iterator variable without a self-documenting name
- An excessively large function
- Inconsistent indentation
- Failure to factor duplicate code into a separate function

### Grading appeals
Submit grading/policy concerns **in writing to `cs224ta@cs.byu.edu` within one week** of the posted score; a TA or instructor will follow up.

## Academic Integrity

### No AI tools
You may **not** use ChatGPT or other AI systems to create answers, solutions, or code for homework, exams, labs, or projects. Doing so should be expected to earn a **zero** on that assignment.

### Honesty & collaboration
- Solutions to homework/labs are readily found online — **using them in any way is cheating** and short-circuits learning. The textbook has sufficient examples; there is real value in working out (and verifying) your own solution.
- **Peer collaboration is encouraged** — work with peers on learning activities — **but each student must do their own work.** Copying in a peer setting is cheating.
- **Safeguard your work:** don't leave it unprotected on lab machines, send it to others, post it online, store it in a shared/public location, or leave it on a whiteboard. Actively providing solutions makes you complicit in cheating.
- If you get caught up in cheating, **meet with the instructor quickly** — don't let shame keep you from making it right.

## Study Habits (recommended)

- **Read the book wisely** and regularly; be prepared for class.
- **Come to class and engage** — phone off, ask good questions, participate in group work; don't do labs/projects during lecture.
- **Find a peer group** and schedule regular times to work together, in and out of class. Working alone is ineffective.
- **Attempt learning activities before the due date** so you can ask targeted questions in class.
- **Keep the Sabbath holy** — the late policy is designed so you aren't penalized for resting on Sunday.
- **Details matter!** Correctness depends on many small details; be patient — mastering them is where much of the learning happens.

> *Teaching philosophy:* a student's intellect is **not fixed** — it grows with experience through honest effort. The TAs and instructors are trained to **guide your learning rather than give answers**, so you learn how to learn.

## TA Support Policy

TAs help you learn — sometimes reading the book with you, sometimes searching concepts together, sometimes having you work problems and giving feedback. **They are trained not to give answers**, but to guide you to discover them. To serve everyone:
- A single student visit is generally **no more than ~20 minutes**.
- Wait **at least ~30 minutes between visits** to internalize concepts first.

Trust the process — learning to learn can feel frustrating, but the staff is committed to your success. Address concerns with the TA policy directly with the instructors.

## University Policies (official)

The official syllabus includes BYU's standard statements in full. Key points and contacts:

- **Statement on Belonging** — we are united by our identity as children of God and strive for a community of belonging where each student's divine potential is the central focus.
- **Honor Code** — students are expected to be honest in all academic work and to follow all Honor Code standards (incl. Dress & Grooming). Questions: Honor Code Office, **801-422-2847**.
- **Sexual Misconduct / Title IX** — BYU prohibits sex discrimination and sexual harassment. Faculty must report incidents that come to their attention. Title IX Coordinator: **t9coordinator@byu.edu**, **801-422-8692**, or report at <https://titleix.byu.edu/report> (24-hr line 1-888-238-1062). Confidential help: Sexual Assault Survivor Advocacy Services, **801-422-9071**, advocate@byu.edu.
- **Students with Disabilities** — contact the University Accessibility Center (UAC), 2170 WSC, **801-422-2767**, for reasonable accommodations.
- **Mental Health** — BYU Counseling and Psychological Services (CAPS), 1500 WSC, **801-422-3035**, offers free, confidential counseling to full-time students.
- **Inappropriate Use of Course Materials** — all course materials are proprietary; do not post, sell, retain, or further disseminate them without the professor's written permission.

This course **adheres strictly to all university policies**; review and be familiar with them as part of the course.