# Minimum ASCII Delete Sum for Two Strings
_update Feb 20 2019, 15:34_

---
[LeetCode](https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/)

Given two strings s1, s2, find the lowest ASCII sum of deleted characters to make two strings equal.

**Example 1:**

    Input: s1 = "sea", s2 = "eat"
    Output: 231
    Explanation: Deleting "s" from "sea" adds the ASCII value of "s" (115) to the sum.
    Deleting "t" from "eat" adds 116 to the sum.
    At the end, both strings are equal, and 115 + 116 = 231 is the minimum sum possible to achieve this.

**Example 2:**

    Input: s1 = "delete", s2 = "leet"
    Output: 403
    Explanation: Deleting "dee" from "delete" to turn the string into "let",
    adds 100[d]+101[e]+101[e] to the sum.  Deleting "e" from "leet" adds 101[e] to the sum.
    At the end, both strings are equal to "let", and the answer is 100+101+101+101 = 403.
    If instead we turned both strings into "lee" or "eet", we would get answers of 433 or 417, which are higher.

**Note:**

1. `0 < s1.length, s2.length <= 1000.`
2. `All elements of each string will have an ASCII value in [97, 122].`

<br/>

## Basic Idea:
* ### DP Solution 
  利用DP的思路求解，我们可以有如下规则：
  ```python
    Input：String a, String b

    dp[i][j] 表示 a[0:i] 和 b[0:j] 之间的 min delete ASCII sum
    
    Base case:
        dp[0][j] 表示a为空，则 dp[0][j]=b[j-1] + dp[0][j-1]
                             dp[i][0]=a[i-1] + dp[i-1][0]
    Induction Rule:
        dp[i][j] = dp[i-1][j-1],          when a[i-1]==b[j-1]
                 = min {
                     dp[i-1][j] + a[i-1],
                     dp[i][j-1] + b[j-1],             , otherwise
                     dp[i-1][j-1] + a[i-1] + b[j-1]
                    }
    
    时间复杂度 O(m*n), 空间 O(m*n), 可以优化到 O(min(m,n))使用滚动数组。
  ```
  #### Java Code:
  ```java
    class Solution {
        public int minimumDeleteSum(String s1, String s2) {
            int M = s1.length() + 1, N = s2.length() + 1;
            int[][] dp = new int[M][N];
            for (int i = 1; i < M; ++i) {
                dp[i][0] = (int)s1.charAt(i - 1) + dp[i - 1][0];
            }
            for (int i = 1; i < N; ++i) {
                dp[0][i] = (int)s2.charAt(i - 1) + dp[0][i - 1];
            }
            for (int i = 1; i < M; ++i) {
                for (int j = 1; j < N; ++j) {
                    char a = s1.charAt(i - 1);
                    char b = s2.charAt(j - 1);
                    if (a == b) {
                        dp[i][j] = dp[i - 1][j - 1];
                    } else {
                        dp[i][j] = Math.min(dp[i - 1][j] + a, dp[i][j - 1] + b);
                        dp[i][j] = Math.min(dp[i][j], dp[i -1][j - 1] + a + b);
                    }
                }
            }
            return dp[M-1][N-1];
        }
    }
  ```

* ### DFS with cache
  DFS，每次删去前面字母，利用hashmap做cache。
  ```java
    class Solution {
        private Map<String, Integer> cache = new HashMap<>();
        
        public int minimumDeleteSum(String s1, String s2) {
            return dfs(s1, s2);
        }
        
        private int dfs(String s1, String s2) {
            if (s1.equals(s2)) return 0;
            String key = s1 + "," + s2;
            if (cache.containsKey(key)) return cache.get(key);
            
            int ret = 999999;
            if (s1.length() == 0) {
                ret = getAsciiSum(s2);
            } else if (s2.length() == 0) {
                ret = getAsciiSum(s1);
            } else if (s1.charAt(0) == s2.charAt(0)) {
                ret = dfs(s1.substring(1), s2.substring(1));
            } else {
                // 如果都不为空，则考虑删除s1[0],s2[0],both, 三种情况
                String sub1 = s1.substring(1);
                String sub2 = s2.substring(1);
                char c1 = s1.charAt(0), c2 = s2.charAt(0);
                ret = Math.min(ret, dfs(sub1, s2) + c1);
                ret = Math.min(ret, dfs(s1, sub2) + c2);
                ret = Math.min(ret, dfs(sub1, sub2) + c1 + c2);
            }
            cache.put(key, ret);
            return ret;
        }
        
        int getAsciiSum(String s) {
            int ret = 0;
            for (char c : s.toCharArray()) ret += c;
            return ret;
        }
    }
  ```
