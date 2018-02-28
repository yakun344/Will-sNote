# Pow(x, n) 
_update Feb 28, 2018  16:38_

---
[LeetCode](https://leetcode.com/problems/powx-n/description/)

Implement pow(x, n).

**Example 1:**

    Input: 2.00000, 10
    Output: 1024.00000
    
**Example 2:**
    
    Input: 2.10000, 3
    Output: 9.26100
    
    
## Basic Idea:
利用 `n^2a = n^a * n^a`，可以实现 `log(n)` 时间复杂度的解法。但是要注意当 n 为负数的时候，要做底倒指反的预处理。

* ### Recursive Solution (Java):
```java
    class Solution {
        public double myPow(double x, int n) {
            if (x == 0 || x == 1) return x;
            else if (n == 0) return 1;
            
            if (n < 0) {
                return helper(1 / x, -(long)n); // 这里用long是为了处理当n为 -2147483648 的情况，避免overflow
            } else {
                return helper(x, n);
            }
        }
        
        private double helper(double x, long n) {
            if (n == 1) return x;
            double pow = helper(x, n / 2);
            if (n % 2 != 0) {
                return x * pow * pow;
            } else {
                return pow * pow;   
            }
        }
    }
```

* ### Iterative Solution (Java):
```java
    class Solution {
        public double myPow(double x, int n) {
            if (x == 0 || x == 1) return x;
            else if (n == 0) return 1;
            
            long N = n;
            if (n < 0) {
                x = 1 / x;
                N = -(long)n;
            }
            
            double ret = 1, curr = x;
            for (long i = N; i > 0; i /= 2) {
                if (i % 2 == 1) {
                    ret = ret * curr;
                } 
                curr *= curr;
            }
            return ret;
        }
    }
```














