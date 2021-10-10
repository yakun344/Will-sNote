# Merge k Sorted Arrays

_update Aug 23, 2017 15:18_

[LintCode](http://www.lintcode.com/en/problem/merge-k-sorted-arrays/)

Given k sorted integer arrays, merge them into one sorted array.

**Example** Given 3 sorted arrays:

```
[
  [1, 3, 5, 7],
  [2, 4, 6],
  [0, 8, 9, 10, 11]
]
```

return \[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11].

**Challenge ** Do it in O(N log k).

> N is the total number of integers. k is the number of arrays.

## Basic Idea:

基本思想和上一道 [Merge K Sorted Lists](https://will-gxz.gitbooks.io/xiaozheng_algo/content/datastructure-problems/merge-k-sorted-lists.html) 其实是一样的，不同的地方在于这里操作的是数组，我们要跟踪当前位置的话还需要保存一组坐标。在Python中，我们可以直接在pq中存入tuple，而在java中，我们就需要定义一个新的类（queue element class);

## Java Code:

```java
    public class Solution {
        /**
         * @param arrays k sorted integer arrays
         * @return a sorted array
         */

        // 建立一个内部类，存入pq的类型
        private class QElement {
            int val;
            int r;
            int c;
            public QElement(int val, int r, int c) {
                this.val = val;
                this.r = r;
                this.c = c;
            }
        }
        public List<Integer> mergekSortedArrays(int[][] arrays) {
            PriorityQueue<QElement> pq = new PriorityQueue<>(arrays.length, new Comparator<QElement>(){
                @Override
                public int compare(QElement e1, QElement e2) {
                    return e1.val - e2.val;
                }
            });
            // 把每个数组的第一个元素存入pq
            for (int r = 0; r < arrays.length; ++r) {
                if (arrays[r].length == 0) continue;
                pq.offer(new QElement(arrays[r][0], r, 0));
            }
            // 每次取出最小值，将当前最小所在数组的下一个元素存入pq
            List<Integer> res = new ArrayList<>();
            while (! pq.isEmpty()) {
                QElement currMin = pq.poll();
                res.add(currMin.val);
                int r = currMin.r;
                int c = currMin.c;
                if (c + 1 < arrays[r].length) {
                    pq.offer(new QElement(arrays[r][c + 1], r, c + 1));
                }
            }
            return res;
        }
    }
```

## Python Code:

再一次见识到了python的短小精炼；

```python
    # 仍然是利用min heap，但是存放的key为值，value为坐标
    import heapq
    class Solution:
        # @param {int[][]} arrays k sorted integer arrays
        # @return {int[]} a sorted array
        def mergekSortedArrays(self, arrays):
            pq = []
            for r in range(len(arrays)):
                if not arrays[r]:
                    continue
                heapq.heappush(pq, (arrays[r][0], r, 0))
            res = []
            while pq:
                currMin = heapq.heappop(pq)
                res.append(currMin[0])
                r = currMin[1]
                c = currMin[2]
                if c + 1 < len(arrays[r]):
                    heapq.heappush(pq, (arrays[r][c + 1], r, c + 1))
            return res
```
