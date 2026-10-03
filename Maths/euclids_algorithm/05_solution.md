> **Trick:** Replace `(a, b)` with `(b, a % b)` repeatedly. The GCD is preserved at every step. When b hits 0, a is your answer.

## Approach

Loop until `b` is 0. Each iteration: save `b` in a temp variable, update `b` to `a % b`, update `a` to the old `b`. When the loop ends, `a` is the GCD.

## Code

```cpp
class Solution {
public:
    int gcd(int a, int b) {
        while (b != 0) {
            int temp = b;
            b = a % b; // remainder becomes the new b
            a = temp;  // old b becomes the new a
        }
        return a;
    }
};
```

## Dry Run

Input: `a = 48, b = 18`

```
b=18: temp=18, b=48%18=12, a=18  →  (a=18, b=12)
b=12: temp=12, b=18%12=6,  a=12  →  (a=12, b=6)
b=6:  temp=6,  b=12%6=0,   a=6   →  (a=6,  b=0)
b=0:  loop ends

return 6 ✓
```

Input: `a = 7, b = 13` (coprime)

```
b=13: temp=13, b=7%13=7,  a=13  →  (a=13, b=7)
b=7:  temp=7,  b=13%7=6,  a=7   →  (a=7,  b=6)
b=6:  temp=6,  b=7%6=1,   a=6   →  (a=6,  b=1)
b=1:  temp=1,  b=6%1=0,   a=1   →  (a=1,  b=0)
b=0:  loop ends

return 1 ✓
```

## Complexity

**Time:** O(log(min(a, b))) — each step roughly halves the smaller number. Even for inputs in the billions, the loop runs fewer than 60 times.

**Space:** O(1) — just three integer variables, no recursion stack.

## Key Pattern

Euclid's algorithm is the classic example of reducing a problem by replacing it with a simpler version of itself. The insight that `GCD(a, b) = GCD(b, a % b)` means you never need to factor either number — you just keep shrinking until the answer reveals itself.
