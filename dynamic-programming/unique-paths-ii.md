## Unique Paths II
_update Sep 2, 2017  12:52_

---
[LeetCode](https://leetcode.com/problems/unique-paths-ii/description/)

Follow up for "Unique Paths":

Now consider if some obstacles are added to the grids. How many unique paths would there be?

An obstacle and empty space is marked as 1 and 0 respectively in the grid.

**For example,**

    There is one obstacle in the middle of a 3x3 grid as illustrated below.
    
    [
      [0,0,0],
      [0,1,0],
      [0,0,0]
    ]
    The total number of unique paths is 2.

**Note:** m and n will be at most 100.

#### Basic Idea:
这道题就是求在有障碍的情况下，从左上角走到右下角所有可能路径的个数，经典DP题目；

状态转移方程：
```java
    dp[i][j] = 0, if grid[i-1][j-1] == 1 , (因为grid是 0 based)
    dp[i][j] = dp[i-1][j] + dp[i][j-1], else
```

#### Java Code：
