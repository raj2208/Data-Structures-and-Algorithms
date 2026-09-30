# Most Frequent Character

## Problem Statement

Given a string `s`, return the character that appears the most number of times. If multiple characters share the same highest frequency, return the one that appears **first in the string**.

---

## Examples

**Example 1**
```
Input:  s = "hello"
Output: 'l'

h→1  e→1  l→2  o→1
l appears the most.
```

**Example 2**
```
Input:  s = "banana"
Output: 'a'

b→1  a→3  n→2
a appears the most.
```

**Example 3 — Tie**
```
Input:  s = "aabbcc"
Output: 'a'

a→2  b→2  c→2
All tied — return the one that appears first, which is 'a'.
```

---

## Constraints

- `1 <= s.length <= 1000`
- `s` contains only lowercase English letters
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
