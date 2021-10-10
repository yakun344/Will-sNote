# Integer Break

_udpate Jul,30 2017 11:02_

[LeetCode](https://leetcode.com/problems/integer-break/description/)

Given a positive integer n, break it into the sum of at least two positive integers and maximize the product of those integers. Return the maximum product you can get.

```
For example, 
given n = 2, return 1 (2 = 1 + 1); given n = 10, return 36 (10 = 3 + 3 + 4).
```

**Note: **You may assume that n is not less than 2 and not larger than 58.

## Basic Idea:

这是一道dp问题。

**思路1 (bottom-up)：** 我们注意到对 n 来说，可以用一个比他小的数 i 将其分成 `i 和 n - i`，而对于这两部分我们又可以分别考虑**直接使用自身**还是**继续拆分**。于是，我们就有了一个思路，即`dp[n] = 对于所有的划分 i，max(i, dp[i]) * max[n - i, dp[n - i]] 的最大值`。只要维持一个int\[] dp，从小到大计算即可。

**思路2 (memoization)（最初想法）：** `dp[n] = max{ i * dp[n - i] for i in range(1, n) }`，换句话说，就是对于所有的相加等于 n 的和序列，取最大的乘积，用 momoization 的方法保存之前结果。用dfs遍历所有序列。但是要注意当 n = 4 时，`2 * dp[2] == 2 * 1 == 2 != 4`，所以 Base case 要一直写到 `n = 4 的情况`。

## Python Code :

思路1：

```python
    class Solution(object):
        def integerBreak(self, n):
            """
            :type n: int
            :rtype: int
            """
            dp = [0] * (n + 1)
            dp[1] = 1
            for i in range(2, n + 1):
                for j in range(1, i):
                    dp[i] = max(dp[i], (max(j, dp[j]) * max(i - j, dp[i - j])))
            return dp[n]
```

## Java Code:

思路2：

```java
    public class Solution {
        public int integerBreak(int n) {
            // 存放当n == i 时的加数的最大乘积
            int[] dp = new int[n + 1];
            dp[1] = 1;
            // more base cases
            if (n >= 4) {
                dp[2] = 1;
                dp[3] = 3;
                dp[4] = 4;
            }

            return dfs(n, 1, dp);
        }
        // 用dfs找到所有和是remain_sum的路径，并将最大乘积存入dp table.
        // dp数组可以用作visited避免重复工作，如果发现dp[remain_sum] != 0，则说明已经计算过
        private int dfs(int remain_sum, int product, int[] dp) {
            if (dp[remain_sum] != 0) return product * dp[remain_sum]; // 之前已经完成
            if (remain_sum <= 0) return -1; // 此路不通
            int max_product = Integer.MIN_VALUE;
            // 找所有加数序列的乘积的最大值
            for (int i = 1; i < remain_sum; ++i) {
                int prod = dfs(remain_sum - i, product * i, dp);
                max_product = Math.max(max_product, prod);
            }
            dp[remain_sum] = max_product;
            return max_product;
        }
    }
```
