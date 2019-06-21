# Ugly Number II

_update Aug 22,2017 16:25_

[LintCode](http://www.lintcode.com/en/problem/ugly-number-ii/)

Ugly number is a number that only have factors 2, 3 and 5.

Design an algorithm to find the nth ugly number. The first 10 ugly numbers are 1, 2, 3, 4, 5, 6, 8, 9, 10, 12...

**Notice**

Note that 1 is typically treated as an ugly number.

**Example**

```text
If n=9, return 10.
```

**Challenge** O\(n log n\) or O\(n\) time.

## Basic Idea:

**思路1：O\(n\)** [这里](http://bookshadow.com/weblog/2015/08/19/leetcode-ugly-number-ii/) 对于这种思路讲的很好。

我们注意到，其实整个序列的每个数都是由之前的丑数乘以\[2,3,5\]而得到的，关键就在于怎么维持从小到大的增长顺序。我们观察到如下现象：

```text
    1 ------------ 1
    2 ------------ 1 * 2
    3 ------------ 1 * 3
    4 ------------ 2 * 2 / 1 * 4
    5 ------------ 1 * 5 / 5 * 1
    6 ------------ 2 * 3 / 3 * 2
    8 ------------ 2 * 4 / 4 * 2
    9 ------------ 3 * 3 
```

我们注意到，对于乘数\[2,3,5\]分别而言，都是从1开始，沿着\[1,2,3,4,5,6,8,9...\]作为被乘数往后相乘。于是，我们只要为\[2,3,5\]分别跟踪好下一个被乘数（第一个因子），每次比较\[2,3,5\]和其当前因子相乘结果，取最小的作为当前ugly number，然后将其（2，3，5中的一个）当前被乘数向后移动一位（切换到下一个丑数）即可。

这就是O\(n\)的解法。

```python
    class Solution:
        """
        @param: n: An integer
        @return: the nth prime number as description.
        """
        def nthUglyNumber(self, n):
            factor = (2,3,5)
            ugly = [1]
            i, j, k = 0, 0, 0  # i, j, k 分别跟踪 2，3，5 的下一个因子 
            while len(ugly) < n:
                next = min(ugly[i] * 2, ugly[j] * 3, ugly[k] * 5)
                if next == ugly[i] * 2:
                    i += 1
                if next == ugly[j] * 3:
                    j += 1
                if next == ugly[k] * 5:
                    k += 1
                ugly.append(next)
            return ugly[-1]
```

**思路2：** 利用一个 min heap，每次取出最小值，乘上【2，3，5】，再offer，循环n次。这样每次都可以保证最小值产生后加入pq，但是要注意去重。

```java
    public class Solution {
        /*
         * @param n: An integer
         * @return: the nth prime number as description.
         */
        public int nthUglyNumber(int n) {
            // 使用一个min heap，每次取出最小值，乘上【2，3，5】，再offer，循环n次，
            // 最小值就是解，时间复杂度O(nlogn)
            if (n == 1) return 1;
            PriorityQueue<Long> pq = new PriorityQueue<>();
            pq.offer((long)1);
            for (int i = 0; i < n - 1; ++i) {
                long currMin = pq.poll();
                // 需要去重
                while (! pq.isEmpty() && pq.peek() == currMin) pq.poll();
                pq.offer(currMin * 2);
                pq.offer(currMin * 3);
                pq.offer(currMin * 5);
            }
            return (int)(long)(pq.peek());
        }
    }
```

LeetCode python:

```python
    class Solution(object):
        def nthUglyNumber(self, n):
            """
            :type n: int
            :rtype: int
            """        
         # min heap solution
            if n == 1:
                return 1
            pq = [1]        
            for i in range(n - 1):
                currMin = heapq.heappop(pq)
                while pq and pq[0] == currMin:
                    heapq.heappop(pq)
                heapq.heappush(pq, currMin * 2)
                heapq.heappush(pq, currMin * 3)
                heapq.heappush(pq, currMin * 5)
            return pq[0]
```

