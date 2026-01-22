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

## In This Architecture

| Type | Instruction | What it does |
|------|-------------|--------------|
| Scalar | `("alu", ("+", dest, a, b))` | `dest = a + b` (one value) |
| Vector | `("valu", ("+", dest, a, b))` | `dest[0:8] = a[0:8] + b[0:8]` (8 values) |
| Scalar Load | `("load", ("load", dest, addr))` | Load 1 word from memory |
| Vector Load | `("load", ("vload", dest, addr))` | Load 8 consecutive words |

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

To add a scalar (like `10`) to a vector, you first need to "broadcast" it - copy the single value into all 8 slots of a vector:

```python
# scratch[16] contains the scalar value 10
("valu", ("vbroadcast", 24, 16))  # scratch[24:32] = [10,10,10,10,10,10,10,10]
("valu", ("+", 0, 0, 24))         # scratch[0:8] = scratch[0:8] + scratch[24:32]
```

This takes 2 cycles instead of 1, but you only broadcast once and can reuse the broadcasted vector for many operations.

---

## Key Takeaway

Vectorization gives up to **8x speedup** by processing 8 elements in the same time it takes to process 1.
