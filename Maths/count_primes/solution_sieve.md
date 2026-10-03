> **Trick:** You only need to cross out multiples starting from `i*i` — everything below that was already crossed out by a smaller prime. And you only need to run the outer loop up to `sqrt(n)` — any composite number up to n is guaranteed to have a prime factor at or below its square root.

## Two optimisations over the basic sieve

### Optimisation 1 — Start inner loop at `i*i` instead of `2*i`

When you reach `i` in the outer loop, every multiple of `i` that's **smaller than `i*i`** has already been crossed out by a prime smaller than `i`.

Example with `i = 5`:
```
2*5 = 10  → already crossed out by 2
3*5 = 15  → already crossed out by 3
4*5 = 20  → already crossed out by 2
5*5 = 25  → this is the FIRST multiple not yet crossed out
```

So starting at `2*i` just redoes work that's already done. Starting at `i*i` skips straight to the first useful multiple.

---

### Optimisation 2 — Outer loop only needs to run to `sqrt(n)`

If a number `m` is not prime (composite), it can always be written as `a * b` where both `a` and `b` are greater than 1. At least one of them must be ≤ sqrt(m) (if both were > sqrt(m), their product would exceed m — contradiction).

This means: every composite number up to `n` has a prime factor ≤ sqrt(n). So any composite will have been crossed out by the time the outer loop reaches sqrt(n). No point running further.

Example for `n = 100`, `sqrt(100) = 10`:
```
Any composite ≤ 100 has a factor in [2, 10].
So primes 2, 3, 5, 7 (all primes ≤ 10) are enough to cross out every composite ≤ 100.
We never need to run i=11, 13, 17... in the outer loop.
```

---

## Code

```cpp
class Solution {
public:
    int countPrimes(int n) {
        if (n <= 2) return 0;

        vector<bool> prime(n, true);
        prime[0] = prime[1] = false;

        // outer loop only needs to go up to sqrt(n)
        for (int i = 2; i * i < n; i++) {
            if (prime[i]) {
                // start at i*i — all smaller multiples already crossed out
                for (int j = i * i; j < n; j += i) {
                    prime[j] = false;
                }
            }
        }

        int count = 0;
        for (int i = 2; i < n; i++) {
            if (prime[i]) count++;
        }
        return count;
    }
};
```

## Dry Run

Input: `n = 30`  ← `sqrt(30) ≈ 5.47`, so outer loop runs for i = 2, 3, 4, 5

```
i=2: prime[2]=true → cross out 4, 8, 12, 16, 20, 24, 28  (start at 2*2=4)
i=3: prime[3]=true → cross out 9, 15, 21, 27             (start at 3*3=9)
i=4: prime[4]=false → skip
i=5: prime[5]=true → cross out 25                         (start at 5*5=25)

Outer loop stops (6*6=36 > 30)

Remaining primes: 2, 3, 5, 7, 11, 13, 17, 19, 23, 29  →  count = 10 ✓
```

## Complexity

**Time:** O(n log log n) — same asymptotic class as the basic sieve, but the constant is much smaller because far fewer crossing-out operations happen. In practice noticeably faster for large n.

**Space:** O(n) — same boolean array.

## Compared to `solution.md`

| | Basic sieve | Optimised sieve |
|---|---|---|
| Inner loop starts at | `2*i` | `i*i` |
| Outer loop runs to | `n` | `sqrt(n)` |
| Redundant work | Yes — re-crosses already-marked composites | No |
| Correctness | ✓ | ✓ |
