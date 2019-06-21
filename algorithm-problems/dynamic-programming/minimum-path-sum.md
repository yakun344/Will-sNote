# Minimum Path Sum

_update Sep 27 2018, 23:57_

[LeetCode](https://leetcode.com/problems/minimum-path-sum/description/)

Given a `m x n` grid filled with non-negative numbers, find a path from top left to bottom right which minimizes the sum of all numbers along its path.

Note: You can only move either down or right at any point in time.

**Example:**

```text
Input:
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
Output: 7
Explanation: Because the path 1→3→1→1→1 minimizes the sum.
```

## Basic Idea:

典型的坐标型dp问题，`dp[i][j]` 表示在 `matrix[i-1][j-1]` 位置时的最小路径和，然后出口就是右下角。

### Java Code:

```java
class Solution {
    public int minPathSum(int[][] grid) {
        int[][] dp = new int[grid.length + 1][grid[0].length + 1];
        for (int i = 0; i < dp.length; ++i) dp[i][0] = Integer.MAX_VALUE;
        for (int i = 0; i < dp[0].length; ++i) dp[0][i] = Integer.MAX_VALUE;
        dp[1][0] = 0; // start point
        for (int r = 1; r < dp.length; ++r) {
            for (int c = 1; c < dp[0].length; ++c) {
                dp[r][c] = Math.min(dp[r - 1][c], dp[r][c - 1]) + grid[r - 1][c - 1];
            }
        }
        return dp[dp.length - 1][dp[0].length - 1];
    }
}
```

