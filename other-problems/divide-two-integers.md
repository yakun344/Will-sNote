## Divide Two Integers
_update Jul 10, 2017 18:39_

---
[Lintcode](http://www.lintcode.com/en/problem/divide-two-integers/)

Divide two integers without using multiplication, division and mod operator.

If it is overflow, return 2147483647

    Example
    Given dividend = 100 and divisor = 9, return 11.
    
#### Basic Idea:
先举个例子，比如我们有 `20 / 3 = 6 = 3 * (1 * 2^2 + 1 * 2^1 + 0 * 2^0)`，所以我们只要每次求出最大的小于被除数的数，其因数（例如上面的`1 * 2^2`）加入res，然后另被除数减去这个数，再进入下一次循环。考虑到题目不可以使用乘除符号，我们用`3 << 2`来实现`3 * 2 ^ 2`。

具体地，我们用二分法来进行每次查找。

#### Java Code：
这里需要注意判断边界条件，比如是否为负数，还要考虑int的溢出问题。
```java
    public class Solution {
        /**
         * @param dividend the dividend
         * @param divisor the divisor
         * @return the result
         */
        public int divide(int dividend, int divisor) {
            // Write your code here
            if (dividend == 0) return 0;
            if (divisor == 1) return dividend;
            boolean negative = ((dividend < 0 && divisor > 0) || (dividend > 0 && divisor < 0));
            long dvd = Math.abs((long)dividend), dvs = Math.abs((long)divisor);
            long res = 0;
            while (dvd >= dvs) {
                int p = 0, r = 31;
                while (p + 1 < r) {
                    int q = p + (r - p) / 2;
                    if ((dvs << q) <= dvd) p = q;
                    else r = q;
                }
                int n = 0;
                if ((dvs << r) <= dvd) n = r;
                else n = p;
                dvd -= dvs << n;
                res += 1 << n;
            }
            if (res == Integer.MIN_VALUE) res = Integer.MAX_VALUE;
            return negative ? - (int)res : (int)res;
        }
    }
```