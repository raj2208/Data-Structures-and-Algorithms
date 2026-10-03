# Bucket Array

A bucket array is just a regular array that acts as the backbone of a hash map.

Each slot in the array is called a **bucket**. When you store a key-value pair in a hash map, the key gets mapped to an index, and that index tells you which bucket to put the value in.

```
Index:  0     1     2     3     4     5     6     7
      [ ]   [ ]   [raj] [ ]   [ ]   [23]  [ ]   [ ]
```

So if the key `"raj"` maps to index 2, the value goes into bucket 2. If the key `42` maps to index 5, its value goes into bucket 5.

## Why "bucket"?

The name comes from the fact that each slot can hold more than one item (when two keys hash to the same index — more on that in collision). It's not just a single cell; it's a container.

## Size matters

The size of the bucket array determines the range of valid indices. If the array has 10 buckets, valid indices are 0 through 9. The hash function is responsible for making sure every key maps to an index inside this range.
