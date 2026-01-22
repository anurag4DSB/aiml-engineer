# ALU and Execution Engines

## ALU = Arithmetic Logic Unit

A fundamental CPU component that's been around since the 1940s. It's the part of a processor that does math and logic operations.

### What ALU Handles

- **Arithmetic:** `+`, `-`, `*`, `//`, `%`
- **Logic:** `&` (AND), `|` (OR), `^` (XOR)
- **Shifts:** `<<`, `>>`
- **Comparisons:** `<`, `==`

Every CPU has an ALU. Your laptop, phone, even a calculator - all have ALUs.

---

## VALU = Vector ALU

The vector version of ALU - does the same operations but on 8 values simultaneously.

---

## All Engines in This Architecture

| Name | Full Name | Slots/Cycle | Purpose |
|------|-----------|-------------|---------|
| `alu` | Arithmetic Logic Unit | 12 | Scalar math/logic |
| `valu` | Vector ALU | 6 | Vector math/logic (8 elements each) |
| `load` | Load Unit | 2 | Read from memory into scratch |
| `store` | Store Unit | 2 | Write from scratch to memory |
| `flow` | Control Flow | 1 | Branches, jumps, selects, halt |

---

## What "Slots per Cycle" Means

Each engine can execute multiple operations per cycle (up to its slot limit):

```python
# This is ONE cycle - all 3 ALU operations happen simultaneously
{
    "alu": [
        ("+", 0, 1, 2),   # scratch[0] = scratch[1] + scratch[2]
        ("-", 3, 4, 5),   # scratch[3] = scratch[4] - scratch[5]
        ("*", 6, 7, 8),   # scratch[6] = scratch[7] * scratch[8]
    ]
}
```

The ALU engine has 12 slots, so you could pack up to 12 independent ALU operations in a single cycle.

---

## Why Multiple Engines Matter

Different engines work **in parallel**. In one cycle you can:

- Do 12 ALU operations AND
- Do 6 VALU operations AND
- Do 2 loads AND
- Do 2 stores AND
- Do 1 flow operation

Maximum theoretical throughput per cycle: 23 operations.

The baseline implementation uses ~1 operation per cycle. That's why there's so much room for optimization.

---

## Real CPUs Use These Terms

- **Intel/AMD x86**: Has multiple ALUs per core
- **GPUs (NVIDIA/AMD)**: Thousands of ALUs for parallel processing
- **ARM**: ALU is part of the core pipeline

The terminology in this challenge mirrors real processor architecture.
