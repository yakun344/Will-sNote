# 2 Keys Keyboard

_update Aug 4,2017 14:43_

[LeetCode](https://leetcode.com/problems/2-keys-keyboard/description/)

Initially on a notepad only one character 'A' is present. You can perform two operations on this notepad for each step:

Copy All: You can copy all the characters present on the notepad \(partial copy is not allowed\). Paste: You can paste the characters which are copied last time. Given a number n. You have to get exactly n 'A' on the notepad by performing the minimum number of steps permitted. Output the minimum number of steps to get n 'A'.

**Example 1:**

```text
Input: 3
Output: 3
Explanation:
Intitally, we have one character 'A'.
In step 1, we use Copy All operation.
In step 2, we use Paste operation to get 'AA'.
In step 3, we use Paste operation to get 'AAA'.
```

**Note:** The n will be in the range \[1, 1000\].

## Basic Idea:

我们注意到对于一个质数 n，step = n，而对于一个合数，step 就是它所有质因数的和。所以这个问题就缩减到分解质因数的问题了。

## Java Code:

```java
    public class Solution {
        // 求所有质因数的和
        public int minSteps(int n) {
            if (n == 1) return 0;
            int ret = 0;
            for (int i = 2; i < Math.sqrt(n) + 1; ++i) {
                if (n % i == 0) {
                    n = n / i;
                    ret += i;
                    i = 1;
                }
            }
            if (n == 1) return ret;
            return ret + n;
        }
    }
```

