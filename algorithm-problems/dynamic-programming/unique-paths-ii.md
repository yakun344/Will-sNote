# Unique Paths II

_update Sep 2, 2017 12:52_

[LeetCode](https://leetcode.com/problems/unique-paths-ii/description/)

Follow up for "Unique Paths":

Now consider if some obstacles are added to the grids. How many unique paths would there be?

An obstacle and empty space is marked as 1 and 0 respectively in the grid.

**For example,**

```text
There is one obstacle in the middle of a 3x3 grid as illustrated below.

[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
The total number of unique paths is 2.
```

**Note:** m and n will be at most 100.

## Basic Idea:

这道题就是求在有障碍的情况下，从左上角走到右下角所有可能路径的个数，经典DP题目；

状态转移方程：

```java
    dp[i][j] = 0, if grid[i-1][j-1] == 1 , (因为grid是 0 based)
    dp[i][j] = dp[i-1][j] + dp[i][j-1], else
```

## Java Code：

只提供 O\(m \* n\) 时间和空间的解法，可以像上题一样用滚动法优化；

```java
    class Solution {
        public int uniquePathsWithObstacles(int[][] obstacleGrid) {
            int R = obstacleGrid.length;
            int C = obstacleGrid[0].length;
            int[][] dp = new int[R + 1][C + 1];
            for (int r = 1; r <= R; ++r) {
                for (int c = 1; c <= C; ++c) {
                // 初始条件，左上角的path数量为1，如果左上角有障碍，则误解
                    if (r == 1 && c == 1 && obstacleGrid[0][0] != 1) dp[r][c] = 1;
                    else if (obstacleGrid[r - 1][c - 1] == 1) dp[r][c] = 0;
                    else dp[r][c] = dp[r - 1][c] + dp[r][c - 1];
                }
            }
            return dp[R][C];
        }
    }
```

