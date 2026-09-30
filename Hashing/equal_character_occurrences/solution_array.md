> **Trick:** Since input is only lowercase letters, use `int freq[26]` indexed by `c - 'a'` instead of a map — same logic, no hashing overhead.

## Approach

Same two-pass idea as the map solution. Count frequencies in a fixed array of 26 ints. Use the frequency of `s[0]` as the reference, then scan the string again checking every character matches it.

## Code

```cpp
class Solution {
public:
    bool areOccurrencesEqual(string s) {
        int freq[26] = {0}; // index 0='a', index 25='z'

        // pass 1: count frequencies
        for (char c : s)
            freq[c - 'a']++;

        // reference: whatever frequency the first character has
        int value = freq[s[0] - 'a'];

        // pass 2: every character in the string must match the reference
        for (char c : s) {
            if (freq[c - 'a'] != value)
                return false;
        }

        return true;
    }
};
```

## Dry Run

Input: `"abacbc"`

**Pass 1 — fill freq array:**
```
'a' → freq[0]++  →  freq[0] = 1
'b' → freq[1]++  →  freq[1] = 1
'a' → freq[0]++  →  freq[0] = 2
'c' → freq[2]++  →  freq[2] = 1
'b' → freq[1]++  →  freq[1] = 2
'c' → freq[2]++  →  freq[2] = 2
```

**Reference value:**
```
value = freq['a' - 'a'] = freq[0] = 2
```

**Pass 2:**
```
c='a'  freq[0]=2  2 == 2 ✓
c='b'  freq[1]=2  2 == 2 ✓
c='a'  freq[0]=2  2 == 2 ✓
c='c'  freq[2]=2  2 == 2 ✓
c='b'  freq[1]=2  2 == 2 ✓
c='c'  freq[2]=2  2 == 2 ✓
```

Result: `true` ✓

## Complexity

**Time:** O(n) — two linear passes over the string.

**Space:** O(1) — always exactly 26 integers, fixed regardless of input size.

## Key Pattern

`c - 'a'` maps any lowercase letter to 0–25, turning character lookups into direct array indexing. No hashing, no collisions, and the array size is always constant — prefer this over a map whenever the character set is small and known upfront.
