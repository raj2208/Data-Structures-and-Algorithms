> **Trick:** Once you have the GCD, LCM is just one formula away: `LCM(a, b) = (a / GCD) * b`. Divide before multiplying to avoid integer overflow.

## The formula

```
LCM(a, b) = (a * b) / GCD(a, b)
```

Why this works: `a * b` counts every factor from both numbers. Dividing by the GCD removes the factors they share (since those were double-counted). What's left is the smallest number both divide into.

**Example:** `a = 12, b = 18`, `GCD = 6`
```
LCM = (12 * 18) / 6 = 216 / 6 = 36
```

### Overflow caution

`a * b` can overflow `int` when `a` and `b` are both close to `10^9`. Safe fix: divide first, then multiply — `(a / GCD) * b`. Since GCD always divides `a` exactly, `a / GCD` is always a whole number.

## Code

```cpp
class Solution {
public:
    long long lcm(int a, int b) {
        int g = a, temp = b;
        // Euclid's algorithm for GCD
        while (temp != 0) {
            int t = temp;
            temp = g % temp;
            g = t;
        }
        // divide first to avoid overflow, then multiply
        return (long long)(a / g) * b;
    }
};
```

## Dry Run

Input: `a = 12, b = 18`

```
GCD via Euclid:
  g=12, temp=18 → t=18, temp=12%18=12, g=18  →  (g=18, temp=12)
  g=18, temp=12 → t=12, temp=18%12=6,  g=12  →  (g=12, temp=6)
  g=12, temp=6  → t=6,  temp=12%6=0,   g=6   →  (g=6,  temp=0)
  temp=0 → GCD = 6

LCM = (12 / 6) * 18 = 2 * 18 = 36 ✓
```

## Complexity

**Time:** O(log(min(a, b))) — entirely dominated by the GCD computation.

**Space:** O(1) — a handful of variables.

## Key Pattern

GCD and LCM are two sides of the same coin. Their relationship — `GCD * LCM = a * b` — means once you have one, the other is a single multiplication and division away. Always compute LCM through GCD rather than brute-forcing multiples.
