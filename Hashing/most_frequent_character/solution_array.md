> **Trick:** Since the input is always lowercase English letters, you don't need a map at all — a fixed array of 26 ints works, indexed by `c - 'a'`.

## Approach

Every lowercase letter maps to a number 0–25 via `c - 'a'`. So `'a'` → 0, `'b'` → 1, ..., `'z'` → 25. Store counts in a plain int array of size 26. Then scan the string a second time to find the max, same tie-breaking logic as the map approach.

## Code

```cpp
class Solution {
public:
    char mostFrequentCharacter(string s) {
        int freq[26] = {0}; // index 0 = 'a', index 25 = 'z'

        // pass 1: count frequencies using array indexing
        for (char c : s)
            freq[c - 'a']++;

        char answer = s[0];
        int maxFreq = 0;

        // pass 2: scan the string to find max, iterating string not array
        // so first occurrence wins on a tie
        for (char c : s) {
            if (freq[c - 'a'] > maxFreq) {
                maxFreq = freq[c - 'a'];
                answer = c;
            }
        }

        return answer;
    }
};
```

## Dry Run

Input: `"banana"`

**Pass 1 — fill freq array:**
```
'b' - 'a' = 1  →  freq[1]++  →  freq[1] = 1
'a' - 'a' = 0  →  freq[0]++  →  freq[0] = 1
'n' - 'a' = 13 →  freq[13]++ →  freq[13] = 1
'a' - 'a' = 0  →  freq[0]++  →  freq[0] = 2
'n' - 'a' = 13 →  freq[13]++ →  freq[13] = 2
'a' - 'a' = 0  →  freq[0]++  →  freq[0] = 3
```

**Pass 2 — scan string:**
```
c='b'  freq[1]=1   1 > 0 ✓  answer='b', maxFreq=1
c='a'  freq[0]=3   3 > 1 ✓  answer='a', maxFreq=3
c='n'  freq[13]=2  2 > 3 ✗
c='a'  freq[0]=3   3 > 3 ✗
c='n'  freq[13]=2  2 > 3 ✗
c='a'  freq[0]=3   3 > 3 ✗
```

Result: `'a'` ✓

## Complexity

**Time:** O(n) — same two passes over the string as the map approach.

**Space:** O(1) — the array is always exactly 26 integers regardless of input size, which is constant.

## Key Pattern

When the input is constrained to a known character set (lowercase letters, digits, ASCII), a fixed-size array beats a hash map — same O(1) lookup, zero hashing overhead, better cache performance. The `c - 'a'` trick is worth memorizing.
