## Sqrt(x)
_update Jul 1, 2017 23:24_

---
[leetcode](https://leetcode.com/problems/sqrtx/#/description)
Implement int sqrt(int x).

Compute and return the square root of x.

#### 思路：
最简单的思路是使用二分法从 1 开始到 x / 2 + 1 进行尝试，更高级一点的思路是用牛顿迭代 Newton's method 。 

##### 二分法 python code：
```python
     class Solution(object):
         def mySqrt(self, x):
             """
             :type x: int
             :rtype: int
             """
             p = 1
             r = x // 2 + 1
             while p <= r:
                 q = (p + r) // 2
                 if q * q <= x < (q + 1) * (q + 1):
                     return q
                 elif q * q < x:
                     p = q + 1
                 else:
                     r = q - 1
             return 0
```

##### Newton's Method Python:
```python
    class Solution(object):
        def mySqrt(self, x):
            """
            :type x: int
            :rtype: int
            """
            if not x: return 0
            last = x / 2 + 1
            curr = -1
            while not int(last) ** 2 <= x < int(last + 1) ** 2:
                curr = (last + x / last) / 2
                last = curr
            return int(last)
```