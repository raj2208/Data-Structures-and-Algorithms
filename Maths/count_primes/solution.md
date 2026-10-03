> **Trick:** Start by assuming every number is prime. Then for each prime you find, cross out all its multiples — they can't be prime. Whatever's left uncrossed at the end is prime. Count them up.

## Approach

This is the **Sieve of Eratosthenes**. Create a boolean array `prime[0..n-1]`, all set to `true` initially. Mark 0 and 1 as false (not prime). Then walk through from 2 onwards — when you find a number still marked true, it's prime: count it, then mark every multiple of it as false. By the end, every index still marked true is a prime number.

The inner loop starts at `2*i` (the first multiple of i that's not i itself) and steps by `i` each time.

## Code

```cpp
class Solution {
public:
    int countPrimes(int n) {
        if (n <= 2) return 0;

        vector<bool> prime(n, true);
        prime[0] = prime[1] = false;
        int count = 0;

        for (int i = 2; i < n; i++) {
            if (prime[i]) {
                count++;
                // cross out all multiples of i — they can't be prime
                for (int j = 2 * i; j < n; j += i) {
                    prime[j] = false;
                }
            }
        }
        return count;
    }
};
```

## Dry Run

Input: `n = 10`

```
Initial: prime = [F, F, T, T, T, T, T, T, T, T]
                  0   1  2  3  4  5  6  7  8  9

i=2: prime[2]=true → count=1, cross out 4,6,8
     prime = [F, F, T, T, F, T, F, T, F, T]

i=3: prime[3]=true → count=2, cross out 6,9
     prime = [F, F, T, T, F, T, F, T, F, F]

i=4: prime[4]=false → skip
i=5: prime[5]=true → count=3, cross out nothing (10 is out of range)
i=6: prime[6]=false → skip
i=7: prime[7]=true → count=4, cross out nothing
i=8,9: both false → skip

return 4 ✓  (primes: 2, 3, 5, 7)
```

## Complexity

**Time:** O(n log log n) — each number gets crossed out at most once per prime factor, and the sum of crossing-out work across all primes up to n works out to this. In practice it's very fast.

**Space:** O(n) — one boolean per number up to n.

## Key Pattern

The Sieve is a classic "mark and skip" technique. Instead of testing each number for primality individually (which would be slow), you propagate information forward: once you know i is prime, you immediately eliminate all its multiples from contention. This is why it's so fast — you do less work the further you go, not more.
