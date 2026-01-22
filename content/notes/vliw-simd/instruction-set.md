# Instruction Set Reference

Complete reference for all instructions available in this architecture.

---

## Instruction Format

Every instruction is a tuple: `(engine, (operation, ...operands))`

Most operands are **scratch addresses** (not values). The scratch space is like a large register file with 1536 slots.

---

## ALU Engine (12 slots/cycle)

Scalar arithmetic and logic operations.

```python
("alu", (op, dest, src1, src2))
# scratch[dest] = scratch[src1] OP scratch[src2]
```

### Available Operations

| Op | Description | Example |
|----|-------------|---------|
| `+` | Addition | `scratch[0] = scratch[1] + scratch[2]` |
| `-` | Subtraction | `scratch[0] = scratch[1] - scratch[2]` |
| `*` | Multiplication | `scratch[0] = scratch[1] * scratch[2]` |
| `//` | Integer division | `scratch[0] = scratch[1] // scratch[2]` |
| `%` | Modulo | `scratch[0] = scratch[1] % scratch[2]` |
| `&` | Bitwise AND | `scratch[0] = scratch[1] & scratch[2]` |
| `\|` | Bitwise OR | `scratch[0] = scratch[1] \| scratch[2]` |
| `^` | Bitwise XOR | `scratch[0] = scratch[1] ^ scratch[2]` |
| `<<` | Left shift | `scratch[0] = scratch[1] << scratch[2]` |
| `>>` | Right shift | `scratch[0] = scratch[1] >> scratch[2]` |
| `<` | Less than (returns 0 or 1) | `scratch[0] = 1 if scratch[1] < scratch[2] else 0` |
| `==` | Equal (returns 0 or 1) | `scratch[0] = 1 if scratch[1] == scratch[2] else 0` |

---

## VALU Engine (6 slots/cycle)

Vector operations on VLEN=8 contiguous elements.

```python
("valu", (op, dest, src1, src2))
# scratch[dest:dest+8] = scratch[src1:src1+8] OP scratch[src2:src2+8]
```

### Available Operations

Same as ALU (`+`, `-`, `*`, `//`, `%`, `&`, `|`, `^`, `<<`, `>>`, `<`, `==`) but operates on 8 elements.

### Special: Broadcast

```python
("valu", ("vbroadcast", dest, src))
# scratch[dest:dest+8] = [scratch[src], scratch[src], ..., scratch[src]]
# Copies one scalar value into all 8 vector slots
```

---

## Load Engine (2 slots/cycle)

Read from memory or load constants.

### Load Constant

```python
("load", ("const", dest, value))
# scratch[dest] = value (immediate constant)
```

### Load from Memory (Scalar)

```python
("load", ("load", dest, addr_scratch))
# scratch[dest] = memory[scratch[addr_scratch]]
# Note: addr_scratch contains the ADDRESS, not the value
```

### Load from Memory (Vector)

```python
("load", ("vload", dest, addr_scratch))
# scratch[dest:dest+8] = memory[addr:addr+8]
# where addr = scratch[addr_scratch]
# Loads 8 consecutive memory words
```

---

## Store Engine (2 slots/cycle)

Write to memory.

### Store to Memory (Scalar)

```python
("store", ("store", addr_scratch, src))
# memory[scratch[addr_scratch]] = scratch[src]
```

### Store to Memory (Vector)

```python
("store", ("vstore", addr_scratch, src))
# memory[addr:addr+8] = scratch[src:src+8]
# where addr = scratch[addr_scratch]
# Stores 8 consecutive memory words
```

---

## Flow Engine (1 slot/cycle)

Control flow operations.

### Select (Conditional Move)

```python
("flow", ("select", dest, cond, true_val, false_val))
# scratch[dest] = scratch[true_val] if scratch[cond] else scratch[false_val]
```

### Vector Select

```python
("flow", ("vselect", dest, cond, true_val, false_val))
# For each i in 0..7:
#   scratch[dest+i] = scratch[true_val+i] if scratch[cond+i] else scratch[false_val+i]
```

### Jump (Unconditional)

```python
("flow", ("jump", target_pc))
# PC = target_pc (jump to instruction at index target_pc)
```

### Conditional Jump

```python
("flow", ("cond_jump", cond_scratch, target_pc))
# if scratch[cond_scratch]: PC = target_pc
```

### Pause

```python
("flow", ("pause",))
# Pause execution (for debugging synchronization)
```

### Halt

```python
("flow", ("halt",))
# Stop execution
```

---

## Important Notes

1. **All effects apply at end of cycle** - reads happen before writes

   ```python
   # This works! Both read old values, then both write
   { "alu": [("swap", a, b, b), ("swap", b, a, a)] }  # Not actual syntax, just concept
   ```

2. **Scratch addresses must be allocated** - Use `alloc_scratch()` in KernelBuilder

3. **Vector operations use contiguous addresses** - `dest` means `dest` through `dest+7`

4. **Memory addresses come from scratch** - Load/store take a scratch address containing the memory address, not the memory address directly
