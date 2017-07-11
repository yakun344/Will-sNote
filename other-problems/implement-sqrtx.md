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
如下代码计算的是double type的精确sqrt(x).
```java
    public class Solution {
        /**
         * @param x a double
         * @return the square root of x
         */
        public double sqrt(double x) {
            // 使用牛顿迭代
            if (x == 0) return 0;
            double it = x;
            while (Math.abs(it * it - x) > 1e-12) {
                it = (it + x / it) / 2;
            }
            return it;
        }
    }
```