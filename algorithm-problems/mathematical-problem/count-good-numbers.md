---
description: 'Jul 4, 2021'
---

# Count Good Numbers

[https://leetcode.com/problems/count-good-numbers/](https://leetcode.com/problems/count-good-numbers/)

![](../../.gitbook/assets/image%20%282%29.png)

### Basic Idea

这道题其实并不复杂，只需要计算`5^numEven * 4^numOdd` 即可，但问题是给定的位数非常大，需要一种可以快速计算乘方的方法。discussion中很多解法都涉及到了binary exponentiation算法，但事实上只需要简单的分治法即可。我们只需要从1次方开始，然后2，4，8，16 ... 每次计算翻倍次数的乘方，最终时间复杂度为 logN.

有递归和iterative两种做法，递归稍简单一些。

### Java Code:

```java
// recursion
class Solution {
    long MOD = (long) 1e9 + 7;
    
    public int countGoodNumbers(long n) {
        if (n == 1) return 5;
        long numEven = (n + 1) / 2;
        long numOdd = n / 2;
        return (int) (pow(5, numEven) * pow(4, numOdd) % MOD);
    }
    
    // 利用公式 a^b = (a^0.5b)^2 递归，时间复杂度为 logN
    private long pow(long a, long b) {    
        if (b == 1) return a;
        long half = pow(a, b / 2);
        long res = half * half % MOD;
        if (b % 2 == 1) {
            return res * a % MOD;
        } else {
            return res;
        }
    }
}

// iterative
class Solution {
    long MOD = (long) 1e9 + 7;
    
    public int countGoodNumbers(long n) {
        if (n == 1) return 5;
        long numEven = (n + 1) / 2;
        long numOdd = n / 2;
        return (int) (pow(5, numEven) * pow(4, numOdd) % MOD);
    }
    
    // Iterative 的方法
    // 每次以两倍扩张，直到剩下部分不足翻倍，然后重新从1开始两倍扩张
    // 例如当b=100，先两倍扩张到64，然后剩下36，再从1开始两倍扩张到32，剩下4，
    // 然后两倍扩张到4，结束
    private long pow(long a, long b) {    
        long res = 1; // 最终结果
        long curr = 1; // 当前的乘方次数
        long p = a; // 当前翻倍乘方过程中的结果
        while (true) {
            if (curr == b) {
                return res * p % MOD;
            }
            if (curr * 2 > b) {
                res = res * p % MOD;
                p = a;
                b = b - curr; // b 为剩下的乘方次数
                curr = 1;
            }
            if (curr * 2 <= b) {
                p = p * p % MOD;
                curr *= 2;
            }
        }
    }
}
```

