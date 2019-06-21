# Factorial Trailing Zeroes

_update Jan 22,2018 10:27_

Given an integer n, return the number of trailing zeroes in n!.

**Note:**  
Your solution should be in logarithmic time complexity.

## Basic Idea:

只要计算阶乘中 5 出现的次数就可以了。每次 5 出现的时候，它与 2 相乘都会产生一个 0。所以只要往复累加 `n /= 5` 的结果就可以了，时间复杂度 `O(log5(N))`；

## Python

```python
class Solution:
    def trailingZeroes(self, n):
        """
        :type n: int
        :rtype: int
        """
        ret = 0
        while n > 0:
            n //= 5
            ret += n
        return ret
```

