# Anthropic Performance Take-Home

A performance optimization challenge for a custom VLIW SIMD architecture simulator, originally used by Anthropic for technical interviews.

## Overview

This challenge involves optimizing a kernel that traverses binary trees while performing hash operations. The goal is to minimize execution cycles on a simulated VLIW SIMD processor.

## Architecture Specifications

The challenge uses a custom VLIW SIMD architecture with five execution engines:

| Engine | Slots/Cycle | Purpose |
|--------|-------------|---------|
| ALU | 12 | Scalar arithmetic |
| VALU | 6 | Vector arithmetic (VLEN=8) |
| Load | 2 | Memory reads |
| Store | 2 | Memory writes |
| Flow | 1 | Control flow |

**Key Parameters:**

- **SCRATCH_SIZE:** 1536 32-bit words (register file)
- **Vector Length (VLEN):** 8 elements per vector operation
- **Batch Size:** 256 items processed per round
- **Rounds:** 16 iterations total

## Performance Benchmarks

| Cycles | Description |
|--------|-------------|
| 147,734 | Baseline (unoptimized scalar) |
| 18,532 | Updated starting point (2-hour version) |
| 1,790 | Best human ~2hr / Claude Opus 4.5 casual |
| 1,487 | Impressive threshold |
| 1,363 | Best known (Claude Opus 4.5 improved harness) |

## The Problem

Each round processes a batch of 256 items through these steps:

1. Load index and value from memory
2. XOR value with tree node value at index
3. Apply 6-stage hash function
4. Branch left (`idx*2+1`) if hash is even, else right (`idx*2+2`)
5. Wrap to root if past tree bounds
6. Store updated index and value

## Optimization Strategies Applied

The optimization journey from 147K to ~1.4K cycles involves:

1. **Vectorization (8x)** - Process 8 batch items simultaneously using VALU
2. **Instruction Packing (up to 23x)** - Fill all engine slots per cycle
3. **Loops** - Replace unrolled code with `cond_jump` loops
4. **Memory Batching** - Use `vload`/`vstore` for 8-element transfers
5. **Constant Caching** - Pre-load constants to scratch space
6. **Hash Pipelining** - Overlap hash stages across elements

Combined theoretical speedup: 100x+ (8x vectorization * ~13x packing efficiency)

## Background Knowledge

For the foundational concepts needed to understand this challenge, see the [VLIW SIMD Architecture Notes](../notes/vliw-simd/index.md):

- [Scalar vs Vector](../notes/vliw-simd/scalar-vs-vector.md) - Understanding data parallelism
- [ALU and Engines](../notes/vliw-simd/alu-and-engines.md) - Execution units
- [VLIW Architecture](../notes/vliw-simd/vliw-architecture.md) - Instruction bundling
- [Instruction Set](../notes/vliw-simd/instruction-set.md) - Available operations
- [Optimization Strategies](../notes/vliw-simd/optimization-strategies.md) - General techniques

## Key Commands

```bash
# Run submission tests (official validation)
python tests/submission_tests.py

# Run cycle count test
python perf_takehome.py Tests.test_kernel_cycles

# Generate and view trace
python perf_takehome.py Tests.test_kernel_trace
python watch_trace.py  # Open browser, click "Open Perfetto"

# Validate tests unchanged (important!)
git diff origin/main tests/
```

## Lessons Learned

1. **VLIW forces explicit parallelism** - You must manually schedule what runs together
2. **Data dependencies are the bottleneck** - Finding independent operations is key
3. **Vectorization is powerful but constrained** - Requires contiguous memory access patterns
4. **Instruction packing requires careful planning** - Scratch space allocation matters
