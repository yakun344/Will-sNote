# Guess Number Higher or Lower II

_update Aug 7,2017 15:10_

[LeetCode](https://leetcode.com/problems/guess-number-higher-or-lower-ii/description/)

We are playing the Guess Game. The game is as follows:

I pick a number from 1 to n. You have to guess which number I picked.

Every time you guess wrong, I'll tell you whether the number I picked is higher or lower.

However, when you guess a particular number x, and you guess wrong, you pay $x. You win the game when you guess the number I picked.

**Example:**

```text
n = 10, I pick 8.

First round:  You guess 5, I tell you that it's higher. You pay $5.
Second round: You guess 7, I tell you that it's higher. You pay $7.
Third round:  You guess 9, I tell you that it's lower. You pay $9.

Game over. 8 is the number I picked.

You end up paying $5 + $7 + $9 = $21.
```

Given a particular n ≥ 1, find out how much money you need to have to guarantee a win.

## Basic Idea:

[**这里**](http://www.1point3acres.com/bbs/thread-197552-1-1.html) 有一些分析。 **思路1：**记忆化搜索 首先根据观察，有如下规律：

对于给定范围 \[i, j\] 而言，对于每个 `k （i <= k <= j）`，都有如下情况：

1. 当前 k 就是所求数字，则 cost = 0；
2. 所求数字比 k 小，则 cost = k + cost\(i, k-1\);
3. 所求数字比 k 大，则 cost = k + cost\(k+1, j\);

最终的 cost\(i, j\) 就是当前 k ，以上三种情况的最大值。考虑所有 k 的情况之后，取最小值就是所求的解。

事实上，在搜索过程中，相当于我们每次都只考虑当前 k 不是 target 的情况（除非i==j），所以我们认为target一定在k的左边或者右边。

Python Code:

```python
    class Solution(object):
        def getMoneyAmount(self, n):
            """
            :type n: int
            :rtype: int
            """
            dp = [[None for i in range(n + 1)] for i in range(n + 1)]
            return self.dfs(1, n, dp)


        # int[][] dp
        def dfs(self, left, right, dp):
            if left >= right:
                return 0
            if dp[left][right] != None:
                return dp[left][right]
            minRes = float('INF')   
            res = 0   
            for i in range(left, right + 1):
                l = self.dfs(left, i - 1, dp) + i
                r = self.dfs(i + 1, right, dp) + i
                res = max(l, r)
                minRes = min(minRes, res)
            dp[left][right] = minRes
            return minRes
```

