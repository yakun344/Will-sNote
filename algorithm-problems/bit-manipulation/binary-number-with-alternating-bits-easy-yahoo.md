# Binary Number with Alternating Bits \(Easy Yahoo\)

_update May 14,2018 15:23_

[LeetCode](https://leetcode.com/problems/binary-number-with-alternating-bits/description/)

Given a positive integer, check whether it has alternating bits: namely, if two adjacent bits will always have different values.

**Example 1:**

```text
Input: 5
Output: True
Explanation:
The binary representation of 5 is: 101
```

**Example 2:**

```text
Input: 7
Output: False
Explanation:
The binary representation of 7 is: 111.
```

**8Example 3:**

```text
Input: 11
Output: False
Explanation:
The binary representation of 11 is: 1011.
```

**Example 4:**

```text
Input: 10
Output: True
Explanation:
The binary representation of 10 is: 1010.
```

## Basic Idea:

我们注意到所有 1 0 相间的数字的特性，如果我们令 `a ^ (a>>1)` 一定会是一个全部是 1 的数。我们可以通过判断 `t & (t + 1) == 0` 来判断一个数字 t 是否是全部为 1 的。

* **C++ Code:**

  ```cpp
    class Solution {
    public:
        bool hasAlternatingBits(int n) {
            int t = n ^ (n >> 1);
            return (t & (t + 1)) == 0;
        }
    };
  ```

