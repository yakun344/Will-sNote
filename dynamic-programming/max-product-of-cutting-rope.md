# Max Product Of Cutting Rope
_update Jan 28, 2018 23:41_

---
Given a rope with positive integer-length n, how to cut the rope into m integer-length parts with length `p[0], p[1], ...,p[m-1]`, in order to get the maximal product of `p[0]*p[1]* ... *p[m-1]?` **m is determined by you** and must be greater than 0 (at least one cut must be made). Return the **max product you can have**.

**Assumptions**

* n >= 2

**Examples**

    n = 12, the max product is 3 * 3 * 3 * 3 = 81
      (cut the rope into 4 pieces with length of each is 3).
      
<br>

## Basic Idea:
这道题要求切绳子，使得所有段长度的乘积最大，至少切一刀。使用dp的思路考虑，对于长度为 m 的绳子，我们有如下分析：
```c
    input: m
    Induction Rule：
      dp[i] 表示长度为 i 的绳子可被切割的最大乘积，即子问题的解；
      dp[i] = max{ left * (i-left), dp[left] * (i-left) } 
              for left from 1 to i;
    Base Case:
      dp[1] = 0
```

* #### Java Code:
```java
    public class Solution {
      public int maxProduct(int length) {
        int[] dp = new int[length + 1];
        dp[1] = 0;
        for (int i = 2; i <= length; ++i) {
          int max = 0;
          for (int j = 1; j < i; ++j) {
            max = Math.max(max, Math.max(j * (i - j), dp[j] * (i - j)));
          }
          dp[i] = max;
        }
        return dp[length];
      }
    }
```
    
      
      
      
      
      