# Scalar vs Vector Operations

## Core Concept

**Scalar** = One value at a time

```python
# Scalar: Process one number
a = 5
b = a + 10  # Result: 15
```

**Vector** = Multiple values at once (in this architecture, 8 values)

```python
# Vector: Process 8 numbers simultaneously
a = [5, 6, 7, 8, 9, 10, 11, 12]      # 8 values
b = a + 10                            # One instruction!
# Result: [15, 16, 17, 18, 19, 20, 21, 22]
```

**Why it matters:** The vector operation takes **1 cycle** to do what would take **8 cycles** with scalar operations.

---

## Real-World Analogy

- **Scalar (cashier):** One customer, scan items one by one
- **Vector (warehouse):** Forklift picks up 8 boxes at once

---

## Common SIMD Instruction Patterns

| Type | Operation | What it does |
|------|-----------|--------------|
| Scalar | `add dest, a, b` | `dest = a + b` (one value) |
| Vector | `vadd dest, a, b` | `dest[0:N] = a[0:N] + b[0:N]` (N values) |
| Scalar Load | `load dest, addr` | Load 1 word from memory |
| Vector Load | `vload dest, addr` | Load N consecutive words |

Note: N is the vector length (commonly 4, 8, or 16 depending on architecture).

---

## Visual Example

Adding 10 to values at memory addresses 100-107:

### Scalar approach (8 cycles minimum)

```text
Cycle 1: load mem[100] -> scratch[0]
Cycle 2: add scratch[0] + 10 -> scratch[0]
Cycle 3: store scratch[0] -> mem[100]
Cycle 4: load mem[101] -> scratch[0]
Cycle 5: add scratch[0] + 10 -> scratch[0]
Cycle 6: store scratch[0] -> mem[101]
... repeat for 102-107 ...
```

### Vector approach (3 cycles)

```text
Cycle 1: vload mem[100:108] -> scratch[0:8]     # Load all 8 at once
Cycle 2: vadd scratch[0:8] + 10 -> scratch[0:8] # Add to all 8 at once
Cycle 3: vstore scratch[0:8] -> mem[100:108]    # Store all 8 at once
```

---

## Broadcasting

**The problem:** Vector ALUs operate on vectors, not mixed scalar+vector. You can't directly add a single number to a vector.

```text
[5, 6, 7, 8, 9, 10, 11, 12] + 10 = ???  # Can't mix vector + scalar directly
```

**The solution:** "Broadcast" the scalar - replicate it into all slots of a vector first.

```text
Step 1: Broadcast 10 → [10, 10, 10, 10, 10, 10, 10, 10]

Step 2: Now both operands are vectors, so vector addition works:
        [5, 6, 7, 8, 9, 10, 11, 12]
      + [10, 10, 10, 10, 10, 10, 10, 10]
      = [15, 16, 17, 18, 19, 20, 21, 22]
```

**Why it's still fast:** You broadcast once, then reuse that broadcasted vector for many operations. If you're adding `10` to 1000 different vectors, you broadcast once and use it 1000 times.

---

## Key Takeaway

Vectorization gives up to **8x speedup** by processing 8 elements in the same time it takes to process 1.
