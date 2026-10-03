# Famous Hash Codes

Here are a few common hash code strategies — each one is a different way to turn a key into an integer.

---

## 1. Identity Function

If the key is already an integer, just use it as-is.

```
hashCode(23)  →  23
hashCode(7)   →  7
hashCode(100) →  100
```

Simple and fast. Works perfectly for integer keys. The compression function handles fitting it into the array range.

---

## 2. Sum of ASCII Values

If the key is a string, add up the ASCII values of all its characters.

```
hashCode("cat") = ASCII('c') + ASCII('a') + ASCII('t')
               = 99 + 97 + 116
               = 312
```

Easy to compute. But there's a problem...

### The collision problem

Two different words can produce the **same sum**:

```
hashCode("cat") = 99 + 97 + 116 = 312
hashCode("act") = 97 + 99 + 116 = 312
hashCode("tac") = 116 + 97 + 99 = 312
```

All three are different words, but they produce the same hash code — `312`. After compression, they'll land in the same bucket.

This is called a **collision** — two different keys mapping to the same index.

The ASCII sum hash code is weak specifically because it ignores the *order* of characters. Any anagram of a word will always collide with it.

---

## 3. Polynomial Hash Code (better for strings)

To fix the anagram problem, multiply each character's value by a positional weight:

```
hashCode(s) = s[0]·p⁰ + s[1]·p¹ + s[2]·p² + ...
```

Where `p` is a prime number (commonly 31 or 37). Now `"cat"` and `"act"` produce different outputs because the positions matter.

This is what most production hash functions for strings use under the hood.
