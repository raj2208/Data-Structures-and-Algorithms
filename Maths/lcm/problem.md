# Least Common Multiple (LCM)

## Problem Statement

Given two positive integers `a` and `b`, find their **Least Common Multiple (LCM)**.

The LCM of two integers is the smallest positive integer that is divisible by both of them.

---

## Examples

**Example 1**
```
Input:  a = 4, b = 6
Output: 12

Multiples of 4: 4, 8, 12, 16, 20...
Multiples of 6: 6, 12, 18, 24...
Smallest common one: 12
```

**Example 2**
```
Input:  a = 12, b = 18
Output: 36
```

**Example 3 — One divides the other**
```
Input:  a = 5, b = 10
Output: 10
```

**Example 4 — Coprime numbers**
```
Input:  a = 7, b = 13
Output: 91  (7 * 13, since GCD is 1)
```

---

## Constraints

- `1 <= a, b <= 10^9`

---

## Test Cases

| a | b | Expected LCM |
|---|---|---|
| 4 | 6 | 12 |
| 12 | 18 | 36 |
| 5 | 10 | 10 |
| 7 | 13 | 91 |
| 9 | 9 | 9 |
