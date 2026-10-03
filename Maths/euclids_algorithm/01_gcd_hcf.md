# GCD and HCF

GCD and HCF are two names for the exact same thing.

- **GCD** — Greatest Common Divisor
- **HCF** — Highest Common Factor

Both mean: the **largest number that divides both given numbers exactly** (with no remainder).

---

## Example

What is the GCD of 48 and 18?

Find all divisors of each:
```
Divisors of 48:  1, 2, 3, 4, 6, 8, 12, 16, 24, 48
Divisors of 18:  1, 2, 3, 6, 9, 18
```

Common divisors: 1, 2, 3, 6

The **greatest** one is **6**.

So `GCD(48, 18) = 6`.

---

## Why does it matter?

GCD shows up everywhere in math and CS:
- Simplifying fractions (divide numerator and denominator by GCD)
- Finding LCM: `LCM(a, b) = (a * b) / GCD(a, b)`
- Cryptography, modular arithmetic, and more

The naive approach — listing all divisors and finding the largest common one — works but is slow for large numbers. That's where Euclid's algorithm comes in.
