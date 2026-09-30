> **Trick:** Count frequencies with a map, then iterate the *string* (not the map) a second time so the first character wins any tie.

## Approach

Build a frequency map in one pass. Then scan the string again left to right — the first character whose frequency beats the current max becomes the answer. Using strict `>` means a later character with the same count never overwrites it.

## Code

```cpp
class Solution {
public:
    char mostFrequentCharacter(string s) {
        unordered_map<char, int> freq;

        // pass 1: count how many times each character appears
        for (char c : s)
            freq[c]++;

        char answer = s[0];
        int maxFreq = 0;

        // pass 2: scan the string (not the map) so we see characters
        // in the order they appear — this is what handles ties correctly
        for (char c : s) {
            // strict > means we never replace answer on a tie,
            // so the first occurrence always wins
            if (freq[c] > maxFreq) {
                maxFreq = freq[c];
                answer = c;
            }
        }

        return answer;
    }
};
```

## Dry Run

Input: `"aabbcc"`

**Pass 1 — build freq map:**
```
freq = { a:2, b:2, c:2 }
```

**Pass 2 — scan string left to right:**
```
c='a'  freq[a]=2  2 > 0 ✓  answer='a', maxFreq=2
c='a'  freq[a]=2  2 > 2 ✗  (tie, skip)
c='b'  freq[b]=2  2 > 2 ✗  (tie, skip)
c='b'  freq[b]=2  2 > 2 ✗  (tie, skip)
c='c'  freq[c]=2  2 > 2 ✗  (tie, skip)
c='c'  freq[c]=2  2 > 2 ✗  (tie, skip)
```

Result: `'a'` — correct, 'a' appears first.

## Complexity

**Time:** O(n) — we go through the string twice, each pass is linear.

**Space:** O(1) — the map holds at most 26 entries (lowercase letters only), so it never grows with input size.

## Key Pattern

When a problem asks "which element appears the most?" — build a frequency map in one pass, then answer the question in a second pass. Iterating the original array/string instead of the map gives you control over order, which matters whenever ties need to be broken by position.
