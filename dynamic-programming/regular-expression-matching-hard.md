# Regular Expression Matching (Hard)
_update Feb 8, 2018 18:47_

---
[LeetCode](https://leetcode.com/problems/regular-expression-matching/description/)

Implement regular expression matching with support for `'.'` and `'*'`.

```c
'.' Matches any single character.
'*' Matches zero or more of the preceding element.

The matching should cover the entire input string (not partial).

The function prototype should be:
bool isMatch(const char *s, const char *p)

Some examples:
isMatch("aa","a") → false
isMatch("aa","aa") → true
isMatch("aaa","aa") → false
isMatch("aa", "a*") → true
isMatch("aa", ".*") → true
isMatch("ab", ".*") → true
isMatch("aab", "c*a*b") → true
```

### Basic Idea:
如果不考虑 `*` 的情况，我们实际上只需要做一个 linear scan 就可以完成匹配，因为 s 和 p 在这种情况下他们的每个字母都必须一一对应。然而当我们加入对 `*` 的考虑之后，现在就存在了一个需要考虑一个 `*` 要匹配几次的问题。比如，对于 `abbba` 和 `ab*a` 的情况，`b*` 匹配了尽可能多的字符，直到最后一个 a 之前的所有 b 都匹配了。而对于 `abbba` 和 `ab*ba`，`*` 就只能匹配两个 b。

上面的情况说明会出现s和p两string之间不同位置对应进行匹配的情况，于是我们想到使用 dp 的思路求解。Induction Rule 和 Base Case 如下：
```python
  Input:
             i
     S: xxxxxxxxxxxxxx
     p: xxxxxxxxxx
               j
  Induction rule:
     dp[i][j] 表示 s[:i+1] 和 p[:j+1] 是否匹配;
     dp[i][j] 的取值根据不同情况有如下几种，只有其中之一为true，则dp[i][j]为true：
     1. 由 dp[i-1][j-1] 递推而来
        dp[i-1][j-1] && (A[i]==B[j] || B[j]=='.' || (B[j]=='*' && (B[j-1]=='.' || B[j-1]==A[i])))；
     2. 由 dp[i-1][j] 递推而来
        dp[i-1][j] && (B[j]=='*' && (A[i]==A[i-1]==B[j-1] || B[j-1]=='.'))；
         (这种情况下要特别检查当B[j]=='*'，A[i]==A[i-1]时，B[j-1]是否和A[i]相等，
         因为有可能存在‘*’之前的字母一次都没有选用的情况)
     3. 由 dp[i][j-1] 递推而来
        dp[i][j-1] && B[j]=='*' ；
     4. 由 dp[i][j-2] 递推而来
        dp[i][j-2] && B[j]=='*'；
  
  Base case：
     dp[i][0] = False;
     dp[0][0] = True;
     dp[0][j] = dp[0][j-2] && B[j]=='*'; （这里需要特别注意，有可能是B很长，但都是 '*'，一次都没选用）
```

* #### Java Code:
```java
    class Solution {
        public boolean isMatch(String s, String p) {
            if (s.length() == 0 && p.length() == 0) return true;
            else if (p.length() == 0) return false;
            boolean[][] dp = new boolean[s.length() + 1][p.length() + 1];
            dp[0][0] = true;
            // fill in dp[0][j]
            for (int j = 2; j <= p.length(); ++j) {
                dp[0][j] = dp[0][j - 2] && p.charAt(j - 1) == '*';
            }
            // induction
            for (int i = 1; i <= s.length(); ++i) {
                for (int j = 1; j <= p.length(); ++j) {
                    dp[i][j] = dp[i-1][j-1] && (s.charAt(i-1) == p.charAt(j-1) || p.charAt(j-1) == '.' || 
                                                (p.charAt(j-1) == '*' && (p.charAt(j-2) == '.' || s.charAt(i-1) == p.charAt(j-2))))
                        || dp[i-1][j] && (p.charAt(j-1) == '*' && i > 1 && (s.charAt(i-1) == s.charAt(i-2) && 
                                                                            s.charAt(i-1) == p.charAt(j-2) || p.charAt(j-2) == '.'))
                        || dp[i][j-1] && p.charAt(j-1) == '*'
                        || j > 1 && dp[i][j-2] && p.charAt(j-1) == '*';
                }
            }
            return dp[s.length()][p.length()];
        }
    }
```















