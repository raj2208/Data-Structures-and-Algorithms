# Hash Function

A hash function takes a key (could be a string, number, object — anything) and converts it into an index into the bucket array.

```
key  →  [hash function]  →  index
"raj"  →  hash("raj")  →  2
```

## Two parts inside every hash function

A hash function is made up of two steps working together:

---

### 1. Hash Code

The hash code step converts the key into an **integer**.

- Input: any key (string, object, etc.)
- Output: an integer (can be very large, can even be negative)

Two important jobs:
- **Conversion** — turn whatever the key is (a string, an object) into a number so math can be done on it
- **Uniform distribution** — spread the outputs as evenly as possible so keys don't pile up in the same bucket

---

### 2. Compression Function

The compression function takes that big integer from the hash code and **squeezes it into the valid index range** of the bucket array.

- Input: the integer from the hash code step
- Output: a valid index (0 to N-1, where N is the size of the bucket array)

The most common compression function is just modulo:

```
index = hashCode % N
```

If the bucket array has 10 slots, `% 10` ensures the output is always between 0 and 9.

---

## Together

```
key  →  [hash code]  →  big integer  →  [compression]  →  valid index
```

Think of the hash code as doing the hard thinking (turning the key into a number), and the compression function as doing the final adjustment (fitting that number inside the array).
