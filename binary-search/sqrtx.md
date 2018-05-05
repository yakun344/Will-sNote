## Sqrt(x)
_update Jul 1, 2017 23:24_

---
[leetcode](https://leetcode.com/problems/sqrtx/#/description)
Implement int sqrt(int x).

Compute and return the square root of x.

#### 思路：
最简单的思路是使用二分法从 1 开始到 x / 2 + 1 进行尝试，更高级一点的思路是用牛顿迭代 Newton's method 。 

##### 二分法 Java code：
```java
class Solution {
    public int mySqrt(int x) {
        if (x <= 1) return x;
        long l = 1, r = x / 2 + 1;
        while (l + 1 < r) {
            long mid = l + (r - l) / 2;
            if (mid * mid == x) return (int)mid;
            else if (mid * mid < x) l = mid;
            else r = mid;
        }
        if (r * r <= x) return (int)r;
        else return (int)l;
    }
}
```

##### Newton's Method Python:
牛顿迭代公式：
```python
    求 x 的平方根：
    z = (z + x / z) / 2;
    执行多次，z 逐步逼近sqrt(x)。
```

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