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

## Typical VLIW Execution Units

Modern VLIW processors commonly include these types of execution units:

| Unit | Purpose | Typical Parallelism |
|------|---------|---------------------|
| ALU | Scalar arithmetic and logic | 2-16 operations/cycle |
| VALU | Vector arithmetic and logic | 2-8 operations/cycle |
| Load | Memory reads | 1-4 operations/cycle |
| Store | Memory writes | 1-4 operations/cycle |
| Flow | Control flow (branches, jumps) | 1 operation/cycle |

The exact number of slots varies by processor. For example:

- **TI C6x DSPs**: 8 functional units (2 multipliers, 6 ALU-like)
- **Qualcomm Hexagon**: 4 execution slots per cycle
- **Intel Itanium**: Up to 6 instructions per bundle

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

The combined throughput across all engines represents the theoretical maximum operations per cycle. Achieving this requires finding enough independent operations to fill all slots.

---

## Real CPUs Use These Terms

- **Intel/AMD x86**: Has multiple ALUs per core
- **GPUs (NVIDIA/AMD)**: Thousands of ALUs for parallel processing
- **ARM**: ALU is part of the core pipeline

The terminology in this challenge mirrors real processor architecture.
