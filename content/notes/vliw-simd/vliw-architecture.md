# VLIW Architecture

## VLIW = Very Long Instruction Word

A CPU architecture where **you** (the programmer/compiler) explicitly schedule what runs in parallel, rather than the hardware figuring it out.

---

## Traditional CPU vs VLIW

### Traditional CPU (Out-of-Order Execution)

```text
You write:          CPU figures out:
add a, b, c         "I can run these
mul d, e, f          two in parallel"
sub g, h, i         "This one waits"
```

The hardware has complex circuits to detect parallelism at runtime.

### VLIW CPU

```text
You write exactly what happens each cycle:

Cycle 1: { alu: [add a,b,c], [mul d,e,f] }   # Both run together
Cycle 2: { alu: [sub g,h,i] }                # This runs alone
```

No complex hardware needed - you did the scheduling.

---

## The "Instruction Bundle"

In VLIW, each cycle executes an **instruction bundle** - a collection of operations across all engines:

```python
# One instruction bundle = one cycle
{
    "alu": [
        ("+", 0, 1, 2),    # ALU operation 1
        ("*", 3, 4, 5),    # ALU operation 2
    ],
    "valu": [
        ("+", 16, 16, 24), # Vector operation
    ],
    "load": [
        ("load", 10, 11),  # Memory load
    ],
    "store": [
        ("store", 12, 13), # Memory store
    ],
    "flow": [
        ("select", 6, 7, 8, 9),  # Conditional select
    ]
}
```

All of these execute **simultaneously** in one cycle.

---

## Why VLIW

### Advantages

- **Simpler hardware** - No out-of-order execution logic needed
- **Predictable timing** - You know exactly how many cycles things take
- **Power efficient** - Less complex circuitry

### Disadvantages

- **Compiler/programmer burden** - You must find the parallelism
- **Code size** - Bundles can have empty slots (NOPs)
- **Less flexible** - Can't adapt to runtime conditions

---

## VLIW in the Real World

- **Intel Itanium (IA-64)** - Famous VLIW processor (discontinued)
- **Texas Instruments DSPs** - Audio/video processing
- **Qualcomm Hexagon** - Mobile DSP in Snapdragon chips
- **GPUs (partially)** - Some VLIW-like characteristics

---

## Key Insight

The fundamental challenge in VLIW programming is: **How efficiently can you pack operations into instruction bundles?**

- More operations per bundle = fewer cycles = better performance
- The limiting factors are:
  - Engine slot limits (how many operations of each type per cycle)
  - Data dependencies (operations that depend on each other cannot run in parallel)
  - Available independent work in the algorithm

VLIW architectures shift the burden of finding parallelism from hardware (at runtime) to the compiler/programmer (at compile time).

---

## Applying VLIW Concepts

For a hands-on example applying these concepts, see the [Anthropic Performance Take-Home](../../projects/anthropic-performance-takehome.md) project.
