## Paint Fence
_update Jan 25,2018  12:03_

---
[LeetCode](https://leetcode.com/problems/paint-fence/description/)

There is a fence with n posts, each post can be painted with one of the k colors.

You have to paint all the posts such that no more than two adjacent fence posts have the same color.

Return the total number of ways you can paint the fence.

**Note:**  
n and k are non-negative integers.

<br>

### Basic Idea
题目要求不超过两个相邻的fence涂相同颜色，所以相当于第三个fence要么和前面二个不同色，要么和前面一个不同色，所以我们可以有如下 induction rule：
```
    dp[i] 表示到第 i 个 fence 为止的所有可能数量；
    induction rule:
        dp[i] = (k-1) * dp[i-1] + (k-1) * dp[i-2]
    base case:
        dp[0] = k;
        dp[1] = k^2;
```

* #### Java Code:
 ```java
    class Solution {
        public int numWays(int n, int k) {
            if (n == 0 || k == 0) return 0;
            else if (n == 1) return k;
            else if (n == 2) return k * k;
            int[] dp = new int[n];
            dp[0] = k;
            dp[1] = k * k;
            for (int i = 2; i < n; ++i) {
                dp[i] = (k - 1) * (dp[i-1] + dp[i-2]);
            }
            return dp[n - 1];
        }
    }
```