# Optimization Strategies

Techniques to reduce cycle count from the 147,734 baseline.

---

## Strategy 1: Instruction Packing

**Problem:** Baseline puts one instruction per cycle.

**Solution:** Pack multiple independent operations into the same cycle.

### Before (3 cycles)

```python
Cycle 1: { "alu": [("+", 0, 1, 2)] }
Cycle 2: { "alu": [("*", 3, 4, 5)] }
Cycle 3: { "alu": [("-", 6, 7, 8)] }
```

### After (1 cycle)

```python
Cycle 1: { "alu": [("+", 0, 1, 2), ("*", 3, 4, 5), ("-", 6, 7, 8)] }
```

**Potential speedup:** Up to 12x for ALU-heavy code (12 ALU slots per cycle).

---

## Strategy 2: Vectorization

**Problem:** Processing 1 item at a time when batch has 256 items.

**Solution:** Use vector operations to process 8 items simultaneously.

### Before (scalar, 8 items = 8 cycles minimum)

```python
for i in range(8):
    ("alu", ("+", result[i], a[i], b[i]))
```

### After (vector, 8 items = 1 cycle)

```python
("valu", ("+", result_vec, a_vec, b_vec))  # All 8 at once
```

**Potential speedup:** 8x for data-parallel operations.

---

## Strategy 3: Use Loops Instead of Unrolling

**Problem:** Baseline unrolls all 16 rounds x 256 items = 4,096 iterations as separate instructions.

**Solution:** Use `cond_jump` to loop, reducing code size.

### Before (unrolled)

```python
# 4096 copies of the same instructions
body.append(("alu", ("+", ...)))  # item 0
body.append(("alu", ("+", ...)))  # item 1
body.append(("alu", ("+", ...)))  # item 2
# ... 4093 more ...
```

### After (loop)

```python
# Single copy of instructions, executed 4096 times
loop_start = len(self.instrs)
body.append(("alu", ("+", ...)))       # The work
body.append(("alu", ("+", counter, counter, minus_one)))  # Decrement counter
body.append(("flow", ("cond_jump", counter, loop_start))) # Loop back
```

**Benefits:** Smaller code, fits in instruction cache, easier to reason about.

---

## Strategy 4: Constant Caching

**Problem:** Loading the same constant multiple times.

**Solution:** Load constants once at startup, reuse from scratch.

### Before

```python
("load", ("const", tmp, 2))  # Load 2
("alu", ("*", x, x, tmp))    # x * 2
("load", ("const", tmp, 2))  # Load 2 again!
("alu", ("*", y, y, tmp))    # y * 2
```

### After

```python
# Setup (once)
("load", ("const", two_const, 2))

# Use (many times)
("alu", ("*", x, x, two_const))
("alu", ("*", y, y, two_const))
```

**Note:** `KernelBuilder.scratch_const()` already does this caching.

---

## Strategy 5: Memory Access Batching

**Problem:** Loading one value at a time.

**Solution:** Use `vload`/`vstore` to transfer 8 values at once.

### Before (8 loads = 8 cycles minimum)

```python
for i in range(8):
    ("load", ("load", scratch[i], addr[i]))
```

### After (1 vload = 1 cycle)

```python
("load", ("vload", scratch_base, addr_base))  # Loads scratch[base:base+8]
```

**Constraint:** Memory addresses must be consecutive.

---

## Strategy 6: Pipeline the Hash

**Problem:** Hash has 6 stages, each depending on the previous.

**Solution:** While computing hash stage 3 for element A, compute stage 2 for element B, stage 1 for element C.

### Visualization

```text
Cycle 1: [A-stage1]
Cycle 2: [A-stage2] [B-stage1]
Cycle 3: [A-stage3] [B-stage2] [C-stage1]
Cycle 4: [A-stage4] [B-stage3] [C-stage2] [D-stage1]
...
```

**Complexity:** High - requires careful register allocation and dependency tracking.

---

## Strategy 7: Use Different Engines in Parallel

**Problem:** Only using ALU when you could also use VALU, Load, Store simultaneously.

**Solution:** Schedule operations across engines.

### Before

```python
Cycle 1: { "alu": [compute1] }
Cycle 2: { "load": [load1] }
Cycle 3: { "store": [store1] }
```

### After

```python
Cycle 1: { "alu": [compute1], "load": [load1], "store": [store1] }
```

---

## Optimization Priority

For this challenge, the most impactful optimizations are likely:

1. **Vectorization** (8x potential) - Process 8 batch items at once
2. **Instruction packing** (up to 23x potential) - Fill all engine slots
3. **Loops** - Reduce code size, enable other optimizations
4. **Memory batching** - Use vload/vstore

The combination of vectorization + packing can theoretically achieve 100x+ speedup over the baseline.

---

## Data Dependencies - The Constraint

You can only pack operations that are **independent**:

```python
# CANNOT pack - b depends on a
a = x + 1
b = a * 2  # Must wait for 'a'

# CAN pack - no dependencies
a = x + 1
c = y + 1  # Independent of 'a'
```

Finding independent operations to pack is the core challenge of VLIW programming.
