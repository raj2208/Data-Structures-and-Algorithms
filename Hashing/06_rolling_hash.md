# Rolling Hash (Rabin-Karp)

## The Problem: String Pattern Matching

Given a text `T` of length `n` and a pattern `P` of length `m`, find all positions where `P` occurs in `T`.

**Naive approach**: Slide `P` along `T`, comparing character by character at each position.
- Time: O(n * m) — for each of `n-m+1` positions, compare `m` characters

**Can we do better?** Yes, using hashing.

---

## The Core Idea

What if we hash `P` once, then as we slide a window of size `m` along `T`, we compute the hash of each window and compare it to `hash(P)`?

```
Text:    a b c d e f g h i ...
Window:  [a b c]              hash = 7
                [b c d]       hash = 12
                    [c d e]   hash = 7  <-- candidate! compare characters
```

If hashes match, we verify by comparing characters (to handle false positives from collisions).

This is **Rabin-Karp**: O(n + m) expected time.

The key question: how do we compute the hash of each new window **without recomputing from scratch**?

---

## The Rolling Hash Trick

Using the polynomial hash:
```
h(s[0..m-1]) = s[0]*p^(m-1) + s[1]*p^(m-2) + ... + s[m-1]*p^0
```

When the window slides by one position (remove `s[0]`, add `s[m]`):

```
h(s[1..m]) = s[1]*p^(m-1) + s[2]*p^(m-2) + ... + s[m]*p^0

           = (h(s[0..m-1]) - s[0]*p^(m-1)) * p + s[m]
```

In words:
1. Subtract the contribution of the character falling off the left: `s[0] * p^(m-1)`
2. Multiply by `p` (shifts all remaining characters left by one position)
3. Add the new character `s[m]`

This lets each window transition take O(1) instead of O(m).

```
old hash:  s[0]*p^3 + s[1]*p^2 + s[2]*p^1 + s[3]*p^0
step 1:             ( s[1]*p^2 + s[2]*p^1 + s[3]*p^0 )   remove s[0]*p^3
step 2:               s[1]*p^3 + s[2]*p^2 + s[3]*p^1     multiply by p
step 3:               s[1]*p^3 + s[2]*p^2 + s[3]*p^1 + s[4]*p^0  add s[4]
```

---

## Rabin-Karp Implementation in C++

```cpp
#include <string>
#include <vector>

std::vector<int> rabin_karp(const std::string& text, const std::string& pattern) {
    int n = text.size(), m = pattern.size();
    if (m > n) return {};

    const long long BASE = 31;
    const long long MOD  = 1e9 + 9;

    // Precompute BASE^m mod MOD (needed to remove leftmost character)
    long long base_m = 1;
    for (int i = 0; i < m; ++i)
        base_m = base_m * BASE % MOD;

    auto char_val = [](char c) -> long long { return c - 'a' + 1; };

    // Compute hash of pattern and first window of text
    long long pat_hash = 0, win_hash = 0;
    for (int i = 0; i < m; ++i) {
        pat_hash = (pat_hash * BASE + char_val(pattern[i])) % MOD;
        win_hash = (win_hash * BASE + char_val(text[i]))    % MOD;
    }

    std::vector<int> results;

    for (int i = 0; i <= n - m; ++i) {
        if (win_hash == pat_hash) {
            // Hash match — verify to rule out false positives
            if (text.substr(i, m) == pattern)
                results.push_back(i);
        }
        if (i < n - m) {
            // Roll the window: remove text[i], add text[i+m]
            win_hash = (win_hash * BASE
                        - char_val(text[i]) * base_m % MOD
                        + char_val(text[i + m])
                        + 2 * MOD)   // ensure non-negative before mod
                       % MOD;
        }
    }
    return results;
}

// Usage:
// rabin_karp("abcabcabc", "abc") --> {0, 3, 6}
```

### Why `+ 2 * MOD` before `% MOD`?

After the subtraction `- char_val(text[i]) * base_m`, the result may be negative. Adding `2 * MOD` (or `MOD`) before `% MOD` guarantees the result is non-negative regardless. This is a standard modular arithmetic trick in C++.

---

## Time Complexity

| Phase | Time |
|-------|------|
| Initial hash computation | O(m) |
| Sliding window (n-m windows) | O(n) each O(1) |
| Verification on hash match | O(m) per match |
| Total (expected) | O(n + m) |
| Total (worst case) | O(n * m) |

Worst case occurs when every window is a false positive (e.g., text = "aaaa...a", pattern = "aaaa...b"). In practice, with a good hash and large modulus, false positives are extremely rare.

**Using double hashing reduces false positive probability to ~1/MOD²**:
```cpp
std::pair<long long,long long> double_hash(const std::string& s) {
    const long long B1 = 31, M1 = 1e9 + 7;
    const long long B2 = 37, M2 = 1e9 + 9;
    long long h1 = 0, h2 = 0;
    for (char c : s) {
        h1 = (h1 * B1 + (c - 'a' + 1)) % M1;
        h2 = (h2 * B2 + (c - 'a' + 1)) % M2;
    }
    return {h1, h2};
}
```

---

## Rolling Hash for Subarray / Substring Problems

Rolling hash isn't just for exact pattern matching. It's a general technique for any problem involving fixed-length windows over a sequence.

### Fixed-Length Window: Check for Duplicate Substring

Binary search on length `L`, check if any substring of length `L` repeats:

```cpp
bool has_duplicate_substring(const std::string& s, int L) {
    const long long BASE = 31, MOD = 1e9 + 9;

    long long base_L = 1;
    for (int i = 0; i < L; ++i) base_L = base_L * BASE % MOD;

    long long h = 0;
    for (int i = 0; i < L; ++i)
        h = (h * BASE + (s[i] - 'a' + 1)) % MOD;

    std::unordered_set<long long> seen;
    seen.insert(h);

    for (int i = 1; i + L <= (int)s.size(); ++i) {
        h = (h * BASE
             - (s[i-1] - 'a' + 1) * base_L % MOD
             + (s[i+L-1] - 'a' + 1)
             + 2 * MOD) % MOD;
        if (seen.count(h)) return true;
        seen.insert(h);
    }
    return false;
}
```

This is O(n log n) when used with binary search on length.

---

## The Substring Hash Trick: Arbitrary Length Windows

Precompute prefix hashes to get the hash of **any substring in O(1)**. This is one of the most powerful string techniques.

```cpp
struct PrefixHash {
    const long long BASE = 31, MOD = 1e9 + 9;
    std::vector<long long> h, p;

    PrefixHash(const std::string& s) {
        int n = s.size();
        h.resize(n + 1, 0);
        p.resize(n + 1, 1);
        for (int i = 0; i < n; ++i) {
            h[i+1] = (h[i] * BASE + (s[i] - 'a' + 1)) % MOD;
            p[i+1] = p[i] * BASE % MOD;
        }
    }

    // Returns hash of s[l..r] (0-indexed, inclusive)
    long long get(int l, int r) const {
        return (h[r+1] - h[l] * p[r-l+1] % MOD + MOD) % MOD;
    }
};

// Usage:
// PrefixHash ph("abcabc");
// ph.get(0, 2) == ph.get(3, 5)  --> true ("abc" == "abc")
```

This enables O(1) substring comparison. It turns many O(n²) or O(n³) string problems into O(n) or O(n²).

**Example: count distinct substrings of length k**:
```cpp
int count_distinct(const std::string& s, int k) {
    PrefixHash ph(s);
    std::unordered_set<long long> seen;
    for (int i = 0; i + k <= (int)s.size(); ++i)
        seen.insert(ph.get(i, i + k - 1));
    return seen.size();
}
```

---

## Rolling Hash vs KMP

Both solve pattern matching in O(n + m):

| | Rabin-Karp | KMP |
|--|------------|-----|
| Approach | Hashing | Failure function (automaton) |
| Multiple patterns | Natural extension | Aho-Corasick needed |
| 2D patterns | Works naturally | Very complex |
| False positives | Possible (tiny probability) | None |
| Implementation | Simpler | More complex |

**Rabin-Karp shines for**:
- Searching for multiple patterns simultaneously (compute all pattern hashes once)
- 2D pattern matching (hash rows and columns)
- Problems where you need to identify equal substrings across different strings

---

## Key Takeaways

1. Rolling hash lets you slide a window in O(1) per step instead of O(window size)
2. Always use a large prime modulus to minimize false positives; add `MOD` after subtraction to stay non-negative
3. Double hashing further reduces false positive probability to ~1/(MOD₁ × MOD₂)
4. Prefix hash arrays let you compute the hash of any substring in O(1)
5. Rolling hash is especially powerful for 2D problems and multiple pattern search

---

## What's Next

- [07_common_interview_patterns.md](07_common_interview_patterns.md) — Catalog of hashing patterns that appear in Google interviews
