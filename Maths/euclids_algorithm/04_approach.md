# Approach — Iterative Euclid's Algorithm

## The idea

Use the rule `GCD(a, b) = GCD(b, a % b)` in a loop. Each iteration replaces `a` with `b` and `b` with `a % b`. Repeat until `b` becomes 0 — at that point, `a` holds the GCD.

## Step by step

```
a = 48, b = 18

Step 1: temp = 18,  b = 48 % 18 = 12,  a = 18   →  (18, 12)
Step 2: temp = 12,  b = 18 % 12 = 6,   a = 12   →  (12, 6)
Step 3: temp = 6,   b = 12 % 6  = 0,   a = 6    →  (6, 0)

b = 0 → loop ends → return a = 6
```

## Why iterative over recursive?

The recursive version does the same thing but builds up a call stack — one frame per step. For very large inputs (a, b up to 10^9), this is up to ~60 frames deep, which is fine but unnecessary. The iterative version does the same work with just three variables and no stack overhead.
