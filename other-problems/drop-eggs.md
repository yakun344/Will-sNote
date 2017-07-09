## Drop Eggs
_update Jul 9, 2017 21:52_

---
[Lintcode](http://www.lintcode.com/en/problem/drop-eggs/)
    
There is a building of n floors. If an egg drops from the k th floor or above, it will break. If it's dropped from any floor below, it will not break.

You're given two eggs, Find k while minimize the number of drops for the worst case. Return the number of drops in the worst case.


**Clarification**
For n = 10, a naive way to find k is drop egg from 1st floor, 2nd floor ... kth floor. But in this worst case (k = 10), you have to drop 10 times.

Notice that you have two eggs, so you can drop at 4th, 7th & 9th floor, in the worst case (for example, k = 9) you have to drop 4 times.
    
    Example
    Given n = 10, return 4.
    Given n = 100, return 14.

#### Basic Idea:
因为我们只有两颗鸡蛋，所以我们只能用第一颗鸡蛋试出一个范围，然后用第二个鸡蛋一个一个地试过来。比如一共有100层楼梯，我们如果每次用第一个鸡蛋试十层，如果是第9层碎，我们第一颗鸡蛋在10层摔碎，然后第二个从1开始到第9层，共需要10次。但是如果第99层碎，我们则需要试19次。为了使最坏情况尽可能少，我们显然不可以每次都跳过同样多的层数。

我们注意到第一颗鸡蛋每多试一次，我们最终的worst case也就会加1，所以我们只要让第一颗蛋跳过的距离每次减一就可以了。

最终我们只要计算`1 + 2 + 3 + ... + x >= n, 求 x`

#### Java Code:
可以直接逐一试验，这里是用二分法优化实现。
```java
    public class Solution {
        /**
         * @param n an integer
         * @return an integer
         */
        public int dropEggs(int n) {
            // Write your code here
            long p = 1, r = n;
            while (p + 1 < r) {
                long q = (r - p) / 2 + p;
                long times = (1 + q) * q / 2;
                if (times <= n) p = q;
                else r = q;
            }
            if ((1 + p) * p / 2 >= n) return (int)p;
            return (int)r;
        }
    }
```