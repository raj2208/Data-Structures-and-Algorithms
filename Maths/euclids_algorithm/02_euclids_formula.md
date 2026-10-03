# Euclid's Algorithm

## The Key Insight

If `a` and `b` have a common divisor `d`, then `d` also divides `a - b`. And by extension, `d` also divides `a % b` (the remainder when a is divided by b).

So:
```
GCD(a, b) = GCD(b, a % b)
```

You replace the larger number with the remainder, and repeat. The GCD never changes — you're just working with smaller and smaller numbers until the remainder hits zero.

When `b = 0`, you're done — `a` is the GCD.

---

## Traced Example

`GCD(48, 18)`:

```
GCD(48, 18)  →  48 % 18 = 12  →  GCD(18, 12)
GCD(18, 12)  →  18 % 12 = 6   →  GCD(12, 6)
GCD(12, 6)   →  12 % 6  = 0   →  GCD(6, 0)
GCD(6, 0)    →  b = 0, done   →  answer = 6
```

---

## Why does this work?

The replacement `GCD(a, b) → GCD(b, a % b)` preserves the GCD at every step.

Proof sketch: if d divides both a and b, it must divide `a - b`. Since `a % b` is just `a - (some multiple of b)`, d divides that too. So the set of common divisors doesn't change — we're just shrinking the numbers.

---

## Why is it fast?

Each step, at least one of the numbers gets roughly halved (Fibonacci numbers are the worst case). So the algorithm terminates in O(log(min(a, b))) steps — even for numbers in the billions, it finishes in under 60 steps.
