# Maximal Square (Largest Square of 1's)
_update Mar 7, 2018_

---
[LeetCode](https://leetcode.com/problems/maximal-square/description/)

Given a 2D binary matrix filled with 0's and 1's, find the largest square containing only 1's and return its area.

**For example:**
    
    given the following matrix:
    
    1 0 1 0 0
    1 0 1 1 1
    1 1 1 1 1
    1 0 0 1 0
    Return 4.
    
<br>

## Basic Idea
一个边长为 N 的 square 中共有多少个正方形呢？答案是 `O(N^3)` 级的，因为我们共有 `O(N^2)` 个左上顶点，对于每个顶点可以选 `O(N)` 个不同边长。所以 brute force 就是查看所有的正方形，从中找到边长最长的那个，耗时 `O(N^5)`，因为需要考虑 `O(N^3)` 个正方形，对于每个正方形又需要 `O(N^2)` 的时间去验证其是否全部都是 `“1”`。

接下来用 dp 的思路考虑，考虑如下例子：
```c
        1  1  1  
        1  1  1  
        1  1  1
        
        对于如上例子，其中共有4个边长为2的正方形，当我们已经知道右上，左上和左下三个正方形已经valid时，
        只需要保证右下角的数字是 1 就可以了。于是我们就可以得到一个地推关系式，就是包含 dp[i][j] 为
        右下顶点的正方形一定要满足其自身等于 1，然后考虑其左、上、左上方三个位置正方形边长，取最小的加一。
        
        例如：
        matrix:           dp table:
            1  1  0        1  1  0
            1  1  1  ==>   1  2  1
            1  1  1        1  2  ?
        此时就可以填出 "?" 应为  min(2,2,1) + 1 = 2;
```

* ### Java Code:
```java
    class Solution {
        public int maximalSquare(char[][] matrix) {
            if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
                return 0;
            }
            int R = matrix.length, C = matrix[0].length, maxArea = 0;
            int[][] dp = new int[R][C];
            for (int r = 0; r < R; ++r) {
                for (int c = 0; c < C; ++c) {
                    if (r > 0 && c > 0) {
                        // 在周围三个正方形中选择边长最小的，然后新的正方形就是其边长加一
                        dp[r][c] = matrix[r][c] == '1' ? 
                            Math.min(Math.min(dp[r - 1][c], dp[r][c - 1]), dp[r - 1][c - 1]) + 1 : 0;
                    } else {
                        // 考虑上边界和左边界的情况，最大边长为 1
                        dp[r][c] = matrix[r][c] == '1' ? 1 : 0;
                    }
                    maxArea = Math.max(maxArea, dp[r][c] * dp[r][c]);
                }
            }
            return maxArea;
        }
    }
```