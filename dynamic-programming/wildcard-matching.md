# Wildcard Matching
_update Sep 17 2018, 18:20_

---
[LeetCode](https://leetcode.com/problems/wildcard-matching/description/)

Given an input string `(s)` and a pattern `(p)`, implement wildcard pattern matching with support for `'?'` and `'*'`.

`'?'` Matches any single character.
`'*'` Matches any sequence of characters (including the empty sequence).
The matching should cover the entire input string (not partial).

**Note:**

s could be empty and contains only lowercase letters a-z.
p could be empty and contains only lowercase letters a-z, and characters like ? or `*`.

**Example 1:**

    Input:
    s = "aa"
    p = "a"
    Output: false
    Explanation: "a" does not match the entire string "aa".

**Example 2:**

    Input:
    s = "aa"
    p = "*"
    Output: true
    Explanation: '*' matches any sequence.

**Example 3:**

    Input:
    s = "cb"
    p = "?a"
    Output: false
    Explanation: '?' matches 'c', but the second letter is 'a', which does not match 'b'.

**Example 4:**

    Input:
    s = "adceb"
    p = "*a*b"
    Output: true
    Explanation: The first '*' matches the empty sequence, while the second '*' matches the substring "dce".

**Example 5:**

    Input:
    s = "acdcb"
    p = "a*c?b"
    Output: false

<br/>

### Basic Idea
利用dp的思路考虑，有如下规则：

```c
  dp[r][c] 表示从两string开始至 s[r] 和 p[c] 为止是否匹配。

  Base case：
       最左边一列为false，表示如果p中不使用任何字符则一个都无法匹配；
       最上面一列看情况，表示s中不使用任何字符的匹配情况，如果p以*开头，对应位置为true，
    则继续往右看，如果仍然是*，则继续设为true；

  Rule:
       if p[c-1]=='*': dp[r][c] = dp[r-1][c]||dp[r][c-1]
       if p[c-1]=='?': dp[r][c] = dp[r-1][c-1]
       if p[c-1]==其他: dp[r][c] = dp[r-1][c-1] && s[r-1]==p[c-1]
```

#### Java Code:
注意一个细节，如果p中除了`*`的部分比s长，则不可能匹配，直接返回false；
```java
class Solution {
    public boolean isMatch(String s, String p) {
        boolean[][] dp = new boolean[s.length() + 1][p.length() + 1];
        for (int i = 0; i < dp.length; ++i) dp[i][0] = false;
        for (int i = 0; i < dp[0].length; ++i) dp[0][i] = false;
        dp[0][0] = true;
        int len = 0; // p 除了*的长度
        for (int i = 1; i < dp[0].length; ++i) {
            if (p.charAt(i - 1) != '*') len++;
            if (dp[0][i - 1] && p.charAt(i - 1) == '*') {
                dp[0][i] = true;
            }
        }
        if (len > s.length()) return false;

        for (int r = 1; r < dp.length; ++r) {
            for (int c = 1; c < dp[0].length; ++c) {
                char sChr = s.charAt(r - 1);
                char pChr = p.charAt(c - 1);
                if (pChr == '*') {
                    dp[r][c] = dp[r - 1][c] || dp[r][c -1];
                } else if (pChr == '?') {
                    dp[r][c] = dp[r - 1][c - 1];
                } else {
                    dp[r][c] = dp[r - 1][c -1] && sChr == pChr;
                }
            }
        }
        return dp[dp.length - 1][dp[0].length - 1];
    }
}
```
