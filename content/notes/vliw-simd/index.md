# VLIW SIMD Architecture

**VLIW** (Very Long Instruction Word) and **SIMD** (Single Instruction, Multiple Data) are processor architecture techniques that enable parallel execution of multiple operations.

This guide covers the key concepts needed to understand and optimize for VLIW SIMD architectures.

## Documents

1. **[Scalar vs Vector](scalar-vs-vector.md)** - Understanding the difference between processing one value vs eight values at once

2. **[ALU and Engines](alu-and-engines.md)** - What ALU means and the five execution engines in this architecture

3. **[VLIW Architecture](vliw-architecture.md)** - How Very Long Instruction Word architectures work

4. **[Instruction Set Reference](instruction-set.md)** - Complete list of available instructions

5. **[Optimization Strategies](optimization-strategies.md)** - Techniques to reduce cycle count

---

## Key Concepts

| Concept | Description |
|---------|-------------|
| VLIW | Explicit instruction-level parallelism scheduled by compiler/programmer |
| SIMD | Data-level parallelism via vector operations |
| Vector Length | Number of elements processed simultaneously (commonly 4, 8, or 16) |
| Instruction Bundle | Group of operations executed in one cycle |

## Learning Path

Start with [Scalar vs Vector](scalar-vs-vector.md) to understand data parallelism, then proceed through the documents in order.

!!! tip "Related Project"
    These notes were developed while studying the [Anthropic Performance Take-Home](../../projects/anthropic-performance-takehome.md) challenge, which provides a hands-on way to apply these concepts.
