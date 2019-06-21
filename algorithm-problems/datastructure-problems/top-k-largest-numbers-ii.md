# Top k Largest Numbers II

_update Aug 23, 2017 14:38_

[LintCode](http://www.lintcode.com/en/problem/top-k-largest-numbers-ii/)

Implement a data structure, provide two interfaces:

add\(number\). Add a new number in the data structure. topk\(\). Return the top k largest numbers in this data structure. k is given when we create the data structure.

**Example**

```text
    s = new Solution(3);
    >> create a new data structure.
    s.add(3)
    s.add(10)
    s.topk()
    >> return [10, 3]
    s.add(1000)
    s.add(-99)
    s.topk()
    >> return [1000, 10, 3]
    s.add(4)
    s.topk()
    >> return [1000, 10, 4]
    s.add(100)
    s.topk()
    >> return [1000, 100, 10]
```

## Basic Idea:

基本思想就是使用一个大小限定为k的min heap作为priority queue； 注意JAVA中对于pq到list的转换，可以直接利用构造函数；

## Java Code:

```java
    public class Solution {
        private PriorityQueue<Integer> pq;
        private int k;

        public Solution(int k) {
            this.pq = new PriorityQueue<>();
            this.k = k;
        }

        public void add(int num) {
            pq.offer(num);
            if (pq.size() > k) pq.poll();
        }

        public List<Integer> topk() {
            List<Integer> ret = new ArrayList<>(pq);
            Collections.sort(ret, Collections.reverseOrder());
            return ret;
        }
    };
```

## Python Code:

```python
    import heapq 
    class Solution:

        # @param {int} k an integer
        def __init__(self, k):
            self.pq = []
            self.k = k


        # @param {int} num an integer
        def add(self, num):
            heapq.heappush(self.pq, num)
            if len(self.pq) > self.k:
                heapq.heappop(self.pq)


        # @return {int[]} the top k largest numbers
        def topk(self):
            return sorted(self.pq, key = lambda a : -a)
```

