# Triples with Bitwise AND Equal To Zero
_update Feb 2 2019, 21:22_

---
[LeetCode](https://leetcode.com/problems/triples-with-bitwise-and-equal-to-zero/)

Given an array of integers A, find the number of triples of indices `(i, j, k)` such that:

    0 <= i < A.length
    0 <= j < A.length
    0 <= k < A.length
    A[i] & A[j] & A[k] == 0, where & represents the bitwise-AND operator.
 

**Example 1:**
```c
    Input: [2,1,3]
    Output: 12
    Explanation: We could choose the following i, j, k triples:
    (i=0, j=0, k=1) : 2 & 2 & 1
    (i=0, j=1, k=0) : 2 & 1 & 2
    (i=0, j=1, k=1) : 2 & 1 & 1
    (i=0, j=1, k=2) : 2 & 1 & 3
    (i=0, j=2, k=1) : 2 & 3 & 1
    (i=1, j=0, k=0) : 1 & 2 & 2
    (i=1, j=0, k=1) : 1 & 2 & 1
    (i=1, j=0, k=2) : 1 & 2 & 3
    (i=1, j=1, k=0) : 1 & 1 & 2
    (i=1, j=2, k=0) : 1 & 3 & 2
    (i=2, j=0, k=1) : 3 & 2 & 1
    (i=2, j=1, k=0) : 3 & 1 & 2
```

**Note:**

1. `1 <= A.length <= 1000`
2. `0 <= A[i] < 2^16`

<br/>

### Basic Idea:
首先，位操作有一个数学规律：`(a & b) <= min(a, b) if a>=0 && b>=0`。因为对于正数来说，逻辑与的操作只会让 `1` 的个数越来越少。然后我们考虑到这题目的条件，输入数组的长度可能有1000，则brute force时间复杂度为 `O(1000^3)`，这是很大的。而同时又规定 `A[i] < 2^16`，`2^16=65536`，这个值是远小于`1000^2=1e6`的。于是我们可以先用 `O(n^2)` 时间计算每一对数字的逻辑与，它们一定会落在 `[0, 2^16]` 的范围内，更精确一点，一定会落在 `[0, max(A)]`的范围内。所以我们可以用一个长度为 `max(A)+1` 的数组来存放每个 `a & b` 出现的次数，然后再进行一次循环，`for c in A, for m in all a & b, if (c & m)==0, ret+=count[m]`, 这样时间复杂度就是 `O(len(A)^2 + len(A) * max(A))`;

#### Java Code:
这里提供 count 数组长度为 `2^16+1` 的解法，可以优化为 `max(A)+1` 长度的解法。

```java
class Solution {
    public int countTriplets(int[] A) {
        int[] count = new int[(1 << 16) + 1];
        for (int a : A) {
            for (int b : A) {
                count[a & b]++;
            }
        }
        int ret = 0;
        for (int a : A) {
            for (int b = 0; b < count.length; ++b) {
                if ((a & b) == 0) ret += count[b];
            }
        }
        return ret;
    }
}
```
