> **Trick:** Build a frequency map, then use the frequency of `s[0]` as the reference — if any other character doesn't match it, return false.

## Approach

Count how many times each character appears. Pick the frequency of the first character as the expected value. Scan the string again — if any character has a different frequency, the string isn't good.

## Code

```cpp
class Solution {
public:
    bool areOccurrencesEqual(string s) {
        unordered_map<char, int> count;

        // pass 1: build frequency map
        for (char c : s)
            count[c]++;

        // use the first character's frequency as the target
        // every other character must match this
        int value = count[s[0]];

        // pass 2: scan the string and check every character
        for (char c : s) {
            if (count[c] != value)
                return false; // mismatch found, not a good string
        }

        return true;
    }
};
```

## Dry Run

Input: `"aaabb"`

**Pass 1 — freq map:**
```
count = { a:3, b:2 }
```

**Reference value:**
```
value = count[s[0]] = count['a'] = 3
```

**Pass 2 — check each character:**
```
c='a'  count['a']=3  3 == 3 ✓
c='a'  count['a']=3  3 == 3 ✓
c='a'  count['a']=3  3 == 3 ✓
c='b'  count['b']=2  2 == 3 ✗  → return false
```

Result: `false` ✓

## Complexity

**Time:** O(n) — two passes over the string, both linear.

**Space:** O(1) — the map holds at most 26 entries (lowercase letters only), constant regardless of input size.

## Key Pattern

When you need to check if all values in a frequency map are equal, you don't need to compare every pair — just pick one as the reference and verify the rest match it. Scanning the string (not the map) in the second pass keeps it simple and avoids iterating over map entries.
