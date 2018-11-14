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
            
            double res = 1;
            double curr = x;
            while (N > 0) {
                if (N % 2 == 1) {
                    res = res * curr;
                }
                curr *= curr;
                N /= 2;
            }
            return res;
        }
    }
```

---
_update Nov 13, 2018_

### Update, 点一下思路
递归的解法，实际上我们利用的是这个性质： `A^B = (A^2)^(B/2) if B mod 2 = 0, else A^B = (A^2)^((B-1)/2)*A`
```java
class Solution {
    public double myPow(double x, int n) {
        if (n < 0) {
            x = 1 / x;
            n = -n;
        } 
        return helper(x, n);
    }
    
    private double helper(double x, int n) {
        if (x == 0) return 0;
        else if (n == 0) return 1;
        else if (n == 1) return x;
        else if (n % 2 == 0) {
            return helper(x * x, n / 2);
        } else {
            return x * helper(x * x, n / 2);
        }
    }
}
```
而iterative的解法，我们利用的是另一种性质，例如 `5^62 = 5^(32+16+8+4+2) = 5^32 * 5^16 * 5^8 * 5^4 * 5^2`。那么我们在while循环中一次考虑x的`1,2,3,4...` 次方，并且我们将 n 看成 2 进制，每次将 n 右移一位，判断当前位是否需要，如果需要就将当前的 x 的幂次乘入结果。例如对于 `2^10`，`10 = 1010b`，从二进制可以看出 `2^10 = 2^2 * 2^8`，在计算的过程中，我们依次计算2的0次，2次，4次，8次，并且每次验证 n 的二进制形式当前位是否为 1，如果为 1，说明需要当前的 2 的幂次，则将该幂次乘入res。
```java
class Solution {
    public double myPow(double x, int n) {
        if (x == 0) return 0;
        else if (n == 1) return x;
        else if (n == 0) return 1;
        long N = n;
        if (n < 0) {
            x = 1 / x;
            N = -(long)n;
        }
        double curr = x, res = 1;
        while (N > 0) {
            if ((N & 1) == 1) res *= curr;
            curr *= curr; // 计算 x 的 2，4，8，16... 次幂
            N >>= 1; // 依次检查 n 的第 1，2，3，4... bit 是否为 1
        }
        return res;
    }
}
```
