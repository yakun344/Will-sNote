# Rotated Digits \(Easy Google\)

_update 2018-05-16 12:37:48_

[LeetCode](https://leetcode.com/problems/rotated-digits/description/)

X is a good number if after rotating each digit individually by 180 degrees, we get a valid number that is different from X. Each digit must be rotated - we cannot choose to leave it alone.

A number is valid if each digit remains a digit after rotation. 0, 1, and 8 rotate to themselves; 2 and 5 rotate to each other; 6 and 9 rotate to each other, and the rest of the numbers do not rotate to any other number and become invalid.

Now given a positive number N, how many numbers X from 1 to N are good?

**Example:**

```text
Input: 10
Output: 4
Explanation: 
There are four good numbers in the range `[1, 10] : 2, 5, 6, 9`.
Note that 1 and 10 are not good numbers, since they remain unchanged after rotating.
```

**Note:**

* N  will be in range `[1, 10000]`.

## Basic Idea:

首先观察题目，要求数字满足条件翻转180度之后仍然是数字，并且不可以和原本相同，那么我们就知道该数中一定要包含至少一个 `2,5,6,9` 之间的一个数字，并且该数中所有数字都在 `0,1,2,5,6,8,9` 内。然后只要从 1 开始直到 N 逐个判断就好了。

* **C++ Code:**

  ```cpp
    class Solution {
        bool isGood(int num) {
            int _map[10] = {1, 1, 1, 0, 0, 1, 1, 0, 1, 1}; // 表示0125689可以翻转180度
            bool contains2569 = false;
            while (num > 0) {
                int digit = num % 10;
                num /= 10;
                if (! _map[digit]) return false;
                if (digit == 2 || digit == 5 || digit == 6 || digit == 9) contains2569 = true;
            }
            return contains2569;
        }
    public:
        int rotatedDigits(int N) {
            int ret = 0;
            for (int i = 1; i <= N; ++i) {
                ret += isGood(i);
            }
            return ret;
        }
    };
  ```

