# VLIW SIMD Architecture - Learning Guide

This documentation covers the key concepts needed to understand and optimize for VLIW SIMD architectures.

## Documents

1. **[Scalar vs Vector](scalar-vs-vector.md)** - Understanding the difference between processing one value vs eight values at once

2. **[ALU and Engines](alu-and-engines.md)** - What ALU means and the five execution engines in this architecture

3. **[VLIW Architecture](vliw-architecture.md)** - How Very Long Instruction Word architectures work

4. **[Instruction Set Reference](instruction-set.md)** - Complete list of available instructions

5. **[Optimization Strategies](optimization-strategies.md)** - Techniques to reduce cycle count

---

## Quick Reference

### Performance Targets
| Cycles | Description |
|--------|-------------|
| 147,734 | Baseline (unoptimized) |
| 18,532 | Updated starting point |
| 1,790 | Best human ~2hr / Claude Opus 4.5 casual |
| 1,487 | Impressive threshold |
| 1,363 | Best known |

### Engine Slot Limits
| Engine | Slots/Cycle | Purpose |
|--------|-------------|---------|
| alu | 12 | Scalar arithmetic |
| valu | 6 | Vector arithmetic (8 elements) |
| load | 2 | Memory reads |
| store | 2 | Memory writes |
| flow | 1 | Control flow |

### Key Constants
- **VLEN = 8** - Vector length (elements per vector operation)
- **SCRATCH_SIZE = 1536** - Available scratch space (32-bit words)
- **Batch size = 256** - Items processed per round
- **Rounds = 16** - Number of iterations
