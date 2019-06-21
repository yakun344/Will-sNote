# Find Median from Data Stream

_update Jan 24,2018 21:02_

[LeetCode](https://leetcode.com/problems/find-median-from-data-stream/description/)

Median is the middle value in an ordered integer list. If the size of the list is even, there is no middle value. So the median is the mean of the two middle value.

**Examples:** 

```text
[2,3,4] , the median is 3
[2,3], the median is (2 + 3) / 2 = 2.5
```

Design a data structure that supports the following two operations:

void addNum\(int num\) - Add a integer number from the data stream to the data structure. double findMedian\(\) - Return the median of all elements so far.

**For example:**

```text
addNum(1)
addNum(2)
findMedian() -> 1.5
addNum(3)
findMedian() -> 2
```

## Basic Idea:

拿到这道题目我的思路如下：

1. 首先想到维持一个Doubly Linked List，这样每次插入新的数字之后，如果比当前 mid 大，mid 就可以右移，否则可以左移。但是这种方法没法高效地找到每次要插入数字的准确位置；
2. 沿着之前的思路，寻求优化，于是想到我们要做的事情的本质其实是将数据分成两段大小相等的部分，并且维持前半段的最大值max，和后半段的最小值min，每次求中位数的时候，一定来自这两个 min 和 max；
3. 于是，自然想到维持两个大小为 `n/2` 的 heap，前半段用 maxHeap，后半段用 minHeap。在插入新 num 的时候，维持 `0 <= leftMaxHeap.size - rightMinHeap.size <= 1`, 即左边比右边不大超过 1 个元素。这样求中位数的时候，如果两 heap size 相同，就返回两者 peek 的平均，否则就返回 leftMaxHeap.peek\(\)。

**时间复杂度：** 每次插入元素的时候，我们可能需要两次 offer 和一次 poll \(插入和rebalance\), 一共 n 个元素的话，每次addNum操作时间复杂度为 `O(3log(N)) = O(logN)`, 空间复杂度 `O(n)`;   


* **Java Code:**

  ```java
    class MedianFinder {
        private PriorityQueue<Integer> leftMaxHeap;
        private PriorityQueue<Integer> rightMinHeap;

        /** initialize your data structure here. */
        public MedianFinder() {
            leftMaxHeap = new PriorityQueue<>((a, b)->Integer.compare(b, a));
            rightMinHeap = new PriorityQueue<>();
        }

        // 逻辑是先添加num，然后再做 rebalance
        public void addNum(int num) {
            // 判断 num 和左右两个 peek 的大小关系，然后add
            if (leftMaxHeap.isEmpty() || num <= leftMaxHeap.peek()) {
                leftMaxHeap.offer(num);
            } else {
                rightMinHeap.offer(num);
            }
            // rebalance 两个heap，保证左边size比右边不大超过1
            if (leftMaxHeap.size() - rightMinHeap.size() > 1) {
                rightMinHeap.offer(leftMaxHeap.poll());
            } else if (rightMinHeap.size() > leftMaxHeap.size()) {
                leftMaxHeap.offer(rightMinHeap.poll());
            }
        }

        public double findMedian() {
            if (leftMaxHeap.size() == rightMinHeap.size()) {
                return (double)(leftMaxHeap.peek() + rightMinHeap.peek()) / 2;
            } else {
                return leftMaxHeap.peek();
            }
        }
    }
  ```

* **Python Code:**

  python 实现的时候，对于这道题，可以直接把每个key取反来实现 maxHeap:

  ```python
  class MedianFinder(object):

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.leftmax = []
        self.rightmin = []

    def addNum(self, num):
        """
        :type num: int
        :rtype: void
        """
        # 先插入
        if len(self.leftmax) == 0 or num <= -self.leftmax[0]:
            heapq.heappush(self.leftmax, -num)
        else:
            heapq.heappush(self.rightmin, num)
        # 再 rebalance
        if len(self.leftmax) - len(self.rightmin) > 1:
            heapq.heappush(self.rightmin, -heapq.heappop(self.leftmax))
        elif len(self.rightmin) > len(self.leftmax):
            heapq.heappush(self.leftmax, -heapq.heappop(self.rightmin))

    def findMedian(self):
        """
        :rtype: float
        """
        if len(self.leftmax) == len(self.rightmin):
            return (-self.leftmax[0] + self.rightmin[0]) / 2.0
        else:
            return -self.leftmax[0]
  ```

_update 2018-07-21 21:16:02_

### Update Java Solution

这一次的写法应该更好。

1. 先检查两heap的大小，如果 `left.size <= right.size`，则插入左边，否则插入右边。
2. 然后检查左边max会不会大于右边min，如果是，则将两数互换。这样就可以维持两个pq的性质。

```java
class MedianFinder {
    PriorityQueue<Integer> left, right;
    /** initialize your data structure here. */
    public MedianFinder() {
        this.left = new PriorityQueue<>((a, b)->Integer.compare(b, a));
        this.right = new PriorityQueue<>();
    }

    public void addNum(int num) {
        if (left.size() <= right.size()) {
            left.offer(num);
        } else {
            right.offer(num);
        }
        // 检查如果左边最大值比右边最小值更大，则交换两个数
        if (! left.isEmpty() && ! right.isEmpty() && left.peek() > right.peek()) {
            int leftMax = left.poll();
            int rightMin = right.poll();
            left.offer(rightMin);
            right.offer(leftMax);
        }
    }

    public double findMedian() {
        int size = left.size() + right.size();
        if (size % 2 == 0) {
            return ((double)left.peek() + right.peek()) / 2;
        } else {
            return left.peek();
        }
    }
}
```

