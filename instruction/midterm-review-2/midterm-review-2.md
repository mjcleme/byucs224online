

```masteryls
{"id":"d90c6fbb-7dc4-49a8-8ad8-f9de60c8fafe","title":"Mechanism of the PUSH Instruction","type":"multiple-choice"}
In the context of x86 architecture, how does the `push` instruction modify the stack pointer (ESP/RSP) and the system memory?

- [ ] The value is stored at the current address held by the stack pointer, and then the stack pointer is **incremented** to point to the next available slot.
- [ ] The stack pointer is **incremented** to point to the next address, and then the value is written to that new memory location.
- [x] The stack pointer is **decremented** to allocate space, and then the value is stored at the memory location now pointed to by the stack pointer.
- [ ] The value is moved into a temporary buffer register, the stack pointer is cleared, and the value is then written to the base of the stack.
```

```masteryls
{"id":"680dbb1f-0f2a-43ca-bab5-0da353ff4408","title":"Conditional Jump Execution","type":"multiple-choice"}
In assembly language, how does a conditional jump instruction (often denoted as `jcc` or `jxx`, such as `je`, `jne`, or `jg`) determine whether or not to branch to the target label?

- [ ] It performs an internal comparison of two immediate operands passed directly to the jump instruction.
- [ ] It automatically decrements the `ECX` or `RCX` register and jumps if the result is non-zero.
- [x] It checks the state of specific status flags in the flags register (like ZF, SF, or OF) that were set by a previous instruction.
- [ ] It queries the CPU's branch predictor to see if the jump was taken during the last execution cycle.
```

```masteryls
{"id":"fb9b1f4c-9c78-4b73-ad23-7b6103aa2ca1","title":"Mechanics of the ret Instruction","type":"multiple-choice"}
In x86-64 assembly, which of the following best describes the operation performed by the `ret` instruction?

- [ ] It decrements the stack pointer (`RSP`) and pushes the address of the next instruction onto the stack before jumping to a target label.
- [ ] It sets the stack pointer (`RSP`) to the current base pointer (`RBP`) and then pops the old base pointer from the stack to restore the caller's frame.
- [x] It pops the 8-byte value from the top of the stack into the instruction pointer (`RIP`) and increments the stack pointer (`RSP`) accordingly.
- [ ] It performs an unconditional jump to a hardcoded memory address provided as an immediate operand, bypassing the stack entirely.
```

```masteryls
{"id":"f3be464d-1245-44eb-bb73-62b7e4aef856","title":"The Pop Instruction Mechanism","type":"multiple-choice"}
In the context of x86-64 assembly language, which of the following best describes the sequence of operations performed by the `pop %rax` instruction?

- [ ] The stack pointer (`%rsp`) is decremented by 8 bytes, and then the value at that new memory address is copied into `%rax`.
- [ ] The value in `%rax` is copied to the memory address currently held in the stack pointer (`%rsp`), and then the stack pointer is decremented by 8 bytes.
- [x] The value at the memory address currently held in the stack pointer (`%rsp`) is copied into `%rax`, and then the stack pointer is incremented by 8 bytes.
- [ ] The stack pointer (`%rsp`) is incremented by 8 bytes, and then the value at that new memory address is copied into `%rax`.
```

```masteryls
{"id":"f8624129-f99c-4bb1-b30d-7c6203bc2b35","title":"rrmovq Instruction Function","type":"multiple-choice"}
In the Y86-64 instruction set architecture, how does the `rrmovq rA, rB` instruction behave during execution?

- [ ] It loads a 64-bit value from a memory address (calculated using an offset and register `rA`) into register `rB`.
- [x] It copies the 64-bit value directly from source register `rA` and stores it in destination register `rB`.
- [ ] It transfers a 64-bit immediate constant value provided within the instruction into register `rB`.
- [ ] It stores the 64-bit value from register `rA` into a memory location at an address specified by the value in register `rB`.
```
