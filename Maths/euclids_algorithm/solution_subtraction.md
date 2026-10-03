> **Trick:** If two numbers share a common divisor, subtracting the smaller from the larger gives a number that shares the same divisor. Keep subtracting until both numbers are equal — that equal value is the GCD.

## The idea

`GCD(a, b) = GCD(a - b, b)` when `a > b`

So instead of using `%`, just subtract: always subtract the smaller number from the larger one. When both numbers become equal, you have your answer.

## Code

```cpp
class Solution {
public:
    int gcd(int a, int b) {
        while (a != b) {
            if (a > b)
                a = a - b;
            else
                b = b - a;
        }
        return a;
    }
};
```

## Dry Run

Input: `a = 48, b = 18`

```
a=48, b=18  →  a > b  →  a = 48 - 18 = 30
a=30, b=18  →  a > b  →  a = 30 - 18 = 12
a=12, b=18  →  b > a  →  b = 18 - 12 = 6
a=12, b=6   →  a > b  →  a = 12 - 6  = 6
a=6,  b=6   →  a == b →  return 6 ✓
```

## This IS Euclid's algorithm — the original version

The modulo version (`a % b`) is actually just a shortcut for doing subtraction many times at once.

`a % b` = what's left after subtracting `b` from `a` as many times as possible.

So for `a=48, b=18`:
```
Subtraction: 48 → 30 → 12  (subtracted 18 twice, remainder 12)
Modulo:      48 % 18 = 12  (same result, one step)
```

The subtraction version is slower when `a` is much larger than `b` — you'd subtract thousands of times. The modulo version skips all that in one operation. Same algorithm, different speed.

## Complexity

**Time:** O(max(a, b)) worst case — if `a = 1000000` and `b = 1`, you'd subtract 1 a million times. Much slower than the modulo version for large inputs.

**Space:** O(1) — just two variables.
