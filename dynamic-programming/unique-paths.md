## Unique Paths
_update Aug 12, 2017  23:56_

---
[LeetCode](https://leetcode.com/problems/unique-paths/description/)

A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

How many possible unique paths are there?
![](/assets/Screen Shot 2017-08-12 at 11.57.03 PM.png)

Above is a 3 x 7 grid. How many possible unique paths are there?

**Note:** m and n will be at most 100.

#### Basic Idea:
这道题目是Comp160 Greg 讲过的经典dp题目，求path数量。这里记录的目的是贴出了O(m * n) 和 O(2n) 以及最优的 O(n) 三种空间复杂度的经典Code：

**O(m*n) Space Python Code:**
```python
    # O(m*n) space
    
    class Solution(object):
        def uniquePaths(self, m, n):
            """
            :type m: int
            :type n: int
            :rtype: int
            """
            dp = [[0 for i in range(n + 1)] for i in range(m + 1)]
            dp[1][1] = 1
            for r in range(1, m + 1):
                for c in range(1, n + 1):
                    if r == 1 or c == 1:
                        dp[r][c] = 1
                    else:
                        dp[r][c] = dp[r][c - 1] + dp[r - 1][c]
            return dp[m][n]
```
**O(2n) Space Python Code:**
```python
    class Solution(object):
        def uniquePaths(self, m, n):
            """
            :type m: int
            :type n: int
            :rtype: int
            """
            upper = [1] * n
            lower = [1] + [0] * (n - 1)
            for r in range(m - 1):
                for c in range(1, n):
                    lower[c] = lower[c - 1] + upper[c]
                upper = lower
                lower = [1] + [0] * (n - 1)
            return upper[n - 1]
```

**O(n) Space Python Code:**
```python
    class Solution(object):
        def uniquePaths(self, m, n):
            """
            :type m: int
            :type n: int
            :rtype: int
            """
            dp = [1] * n
            for r in range(m - 1):
                for c in range(1, n):
                    dp[c] = dp[c - 1] + dp[c]
            return dp[n - 1]
```