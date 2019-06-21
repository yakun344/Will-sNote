# Longest Palindromic Subsequence

_update Aug 7,2017 0:23_

[LeetCode](https://leetcode.com/problems/longest-palindromic-subsequence/discuss/)

Given a string s, find the longest palindromic subsequence's length in s. You may assume that the maximum length of s is 1000.

**Example 1:**

```text
Input:

"bbbab"
Output:
4
One possible longest palindromic subsequence is "bbbb".
```

**Example 2:**

```text
Input:

"cbbd"
Output:
2
One possible longest palindromic subsequence is "bb".
```

## Basic Idea:

这里有几种不同的思路，由易到难。 **思路1：dfs brute force** 从s的两端开始检查，利用如下转换方程，但是因为这种方法会计算大量重复内容，最终得到TLE。

```java
    // dfs, brute force
    // 利用状态转移方程 dp[i][j] = dp[i+1][j-1] + 2, if s[i]==s[j]
    //                   else   max{ dp[i+1][j], dp[i][j-1] }
    // !!!!!!!! got TLE !!!!!!!!
    public class Solution {
        public int longestPalindromeSubseq(String s) {
            return dfs(s, 0, s.length() - 1);
        }
        private int dfs(String s, int left, int right) {
            if (left == right) return 1;
            if (left > right) return 0; // 无意义
            if (s.charAt(left) == s.charAt(right)) {
                return 2 + dfs(s, left + 1, right - 1);
            }
            return Math.max(dfs(s, left + 1, right), dfs(s, left, right - 1));
        }
    }
```

**思路2：dfs with memoization** 仔细观察了之后我们发现可以用memoization消除重复计算，将时间复杂度从O\(2^n\)优化到O\(n^2\)。

```java
    public class Solution {
        public int longestPalindromeSubseq(String s) {
            return dfs(s, 0, s.length() - 1, new Integer[s.length()][s.length()]);
        }
        private int dfs(String s, int left, int right, Integer[][] dp) {
            if (left == right) return 1;
            if (left > right) return 0; // 无意义
            if (dp[left][right] != null) return dp[left][right];
            if (s.charAt(left) == s.charAt(right)) {
                dp[left][right] = 2 + dfs(s, left + 1, right - 1, dp);
                return dp[left][right];
            }
            dp[left][right] = Math.max(dfs(s, left + 1, right, dp), dfs(s, left, right - 1, dp));
            return dp[left][right];
        }
    }
```

**思路3：bottom-up dp** ![](../../.gitbook/assets/wechatimg10%20%281%29.jpg)

```java
// bottom-up dp solution
public class Solution {
    public int longestPalindromeSubseq(String s) {
        int[][] dp = new int[s.length()][s.length()];
        for (int i = s.length() - 1; i >= 0; --i) {
            dp[i][i] = 1;
            for (int j = i + 1; j < s.length(); ++j) {
                if (s.charAt(i) == s.charAt(j)) {
                    dp[i][j] = 2 + dp[i + 1][j - 1];
                } else {
                    dp[i][j] = Math.max(dp[i + 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[0][s.length() - 1];
    }
}
```

**思路4：转化为求 s 和 s\[::-1\] 的 LCS 的dp问题**

```java
    // 方法1，转化为 s 和 reversed s 求 LCS
    public class Solution {
        public int longestPalindromeSubseq(String s) {
            char[] A = s.toCharArray();
            char[] B = new StringBuilder(s).reverse().toString().toCharArray();
            int[][] dp = new int[A.length + 1][B.length + 1];
            for (int i = 1; i < A.length + 1; ++i) {
                for (int j = 1; j < B.length + 1; ++j) {
                    if (A[i - 1] == B[j - 1]) {
                        dp[i][j] = dp[i - 1][j - 1] + 1;
                    }
                    else {
                        dp[i][j] = Math.max(dp[i][j - 1], dp[i - 1][j]);
                    }
                }
            }
            return dp[A.length][B.length];
        }
    }
```

