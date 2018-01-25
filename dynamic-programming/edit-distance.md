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
        最终 dp[i][j] 的值就是如上三种操作产生的值中最小的，最终结果在右下角。
        
    Base Case:
        dp[0][0] = 0;
        dp[0][j] = j;
        dp[i][0] = i;
```

  * ### Java Code 
  ```java
