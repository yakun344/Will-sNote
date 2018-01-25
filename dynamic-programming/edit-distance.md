# Edit Distance (hard)
_update Jan 25,2018 17:59_

---
[LeetCode](https://leetcode.com/problems/edit-distance/description/)


Given two words word1 and word2, find the minimum number of steps required to convert word1 to word2. (each operation is counted as 1 step.)

You have the following 3 operations permitted on a word:

    a) Insert a character
    b) Delete a character
    c) Replace a character
    
## Basic Idea:
使用二维dp，bottom-up 递推求解。Induction rule 和 base case 如下图：
```python
    Input: string: A, B

    Induction rule:
        dp[i][j] 表示 A[:i] 和 B[:j] 之间的最小 edit distance；
        
        假设我们只考虑对 A 进行操作（只edit一个字符串最终结论是相同的，有文章证明），
        
        当 A[i]==B[j] 的时候，不需要edit，此时 dp[i][j] = dp[i-1][j-1];
        
        当 A[i] != B[j] 时，我们有对A进行 insert, delete, replace 三种选择：
            1. 进行 insert，在A[i]位置插入B[j]，抵消了B[j], 使得 dp[i][j] = dp[i][j-1] + 1;
            2. 进行 delete，删掉A[i], 使得 dp[i][j] = dp[i-1][j] + 1;
            3. 进行 replace, 将 A[i] 换成 B[j], 使得 dp[i][j] = dp[i-1][j-1] + 1;
        最终 dp[i][j] 的值就是如上三种操作产生的值中最小的。
        
    Base Case:
        dp[0][0] = 0;
        dp[0][j] = j;
        dp[i][0] = i;
```

  * ### Java Code 
  左边竖轴对应 word1，上边横轴对应 word2，填格子的顺序为从左到右，从上到下，最终右下角是解，时间复杂度和空间复杂度 `O(n^2)`, 其中空间可以被优化为 `O(short length)`。
  ```java
    class Solution {
        public int minDistance(String word1, String word2) {
            int[][] dp = new int[word1.length() + 1][word2.length() + 1];
            for (int i = 1; i < word2.length() + 1; ++i) {
                dp[0][i] = i;
            }
            for (int i = 1; i < word1.length() + 1; ++i) {
                dp[i][0] = i;
            }
            for (int i = 1; i <= word1.length(); ++i) {
                for (int j = 1; j <= word2.length(); ++j) {
                    char c1 = word1.charAt(i - 1), c2 = word2.charAt(j - 1);
                    if (c1 == c2) {
                        dp[i][j] = dp[i - 1][j - 1];
                    } else {
                        dp[i][j] = Math.min(Math.min(dp[i - 1][j] + 1, dp[i][j - 1] + 1), dp[i - 1][j - 1] + 1);
                    }
                }
            }
            return dp[word1.length()][word2.length()];
        }
    }
  ```