# Palindrome Partitioning II

_update Dec 2, 2020_

[Leetcode](https://leetcode.com/problems/palindrome-partitioning-ii/)

Given a string s, partition s such that every substring of the partition is a palindrome.

Return the minimum cuts needed for a palindrome partitioning of s.

**Example 1:**

```text
Input: s = "aab"
Output: 1
Explanation: The palindrome partitioning ["aa","b"] could be produced using 1 cut.
```

**Example 2:**

```text
Input: s = "a"
Output: 0
```

**Example 3:**

```text
Input: s = "ab"
Output: 1
```

**Constraints:**

```text
1. 1 <= s.length <= 2000
2. s consists of lower-case English letters only.
```

## Basic Idea:

使用DP的思路，我们可以定义`dp[i]` 为从 i 开始到结束最少需要cut几次，那么对于每个j &gt; i, 使得`s[i:j]`可以构成回文串，那么`dp[i] = min(dp[j+1]) for j>i 且 s[i:j] 是回文`。于是这个算法就是从右到左对于每个i，我们检查每个j，更新 `dp[i]`。而对于如何判断 `s[i:j]` 是否是回文，我们可以使用dp来预处理。这样总的时间复杂度为 `O(N^2)`.

## Java Code:

```java
class Solution {
    public int minCut(String s) {
        int N = s.length();
        boolean[][] isP = new boolean[N][N];
        for (int i = N - 1; i >= 0; --i) {
            for (int j = i; j < N; ++j) {
                if (s.charAt(i) == s.charAt(j)
                    && (j - i < 2 || isP[i + 1][j - 1])) {
                    isP[i][j] = true;
                }
            }
        }
        int[] dp = new int[N + 1];
        for (int i = N - 1; i >= 0; --i) {
            dp[i] = Integer.MAX_VALUE;
            for (int j = i; j < N; ++j) {
                if (isP[i][j]) {
                    dp[i] = Math.min(dp[i], dp[j + 1] + 1);
                }
            }
        }
        return dp[0] - 1;
    }
}
```

