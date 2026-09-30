# Most Frequent Character

## Problem Statement

Given a string `s`, find the character that occurs the maximum number of times.

Return the character with the highest frequency.
If multiple characters have the same maximum frequency, return the character that appears **first in the string**.

---

## Examples

**Example 1**
```
Input:  s = "hello"
Output: 'l'

h → 1, e → 1, l → 2, o → 1
l occurs the most.
```

**Example 2**
```
Input:  s = "banana"
Output: 'a'

b → 1, a → 3, n → 2
a occurs the most.
```

**Example 3 — Tie**
```
Input:  s = "aabbcc"
Output: 'a'

a → 2, b → 2, c → 2
All tied — a appears first in the string, so return a.
```

---

## Constraints

- `1 <= s.length <= 1000`
- `s` contains lowercase English letters only
- On a tie, return the character that appears first in the string

---

## Test Cases

| Input | Expected Output |
|-------|----------------|
| `"hello"` | `'l'` |
| `"banana"` | `'a'` |
| `"aabbcc"` | `'a'` |
| `"programming"` | `'r'` |
| `"z"` | `'z'` |
| `"mississippi"` | `'i'` |
