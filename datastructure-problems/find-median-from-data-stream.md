## Find Median from Data Stream （hard，google）
_update Jan 24,2018  21:02_

---
[LeetCode](https://leetcode.com/problems/find-median-from-data-stream/description/)

Median is the middle value in an ordered integer list. If the size of the list is even, there is no middle value. So the median is the mean of the two middle value.

**Examples: **
    
    [2,3,4] , the median is 3
    [2,3], the median is (2 + 3) / 2 = 2.5

Design a data structure that supports the following two operations:

void addNum(int num) - Add a integer number from the data stream to the data structure.
double findMedian() - Return the median of all elements so far.

**For example:**
    
    addNum(1)
    addNum(2)
    findMedian() -> 1.5
    addNum(3) 
    findMedian() -> 2
    
<br>

### Basic Idea:
拿到这道题目我的思路如下：

  1.  首先想到维持一个Doubly Linked List，这样每次插入新的数字之后，如果比当前 mid 大，mid 就可以右移，否则可以左移。但是这种方法没法高效地找到每次要插入数字的准确位置；
  2.  沿着之前的思路，寻求优化，于是想到我们要做的事情的本质其实是将数据分成两段大小相等的部分，并且维持前半段的最大值max，和后半段的最小值min，每次求中位数的时候，一定来自这两个 min 和 max；
  3.  于是，自然想到维持两个大小为 `n/2` 的 heap，前半段用 maxHeap，后半段用 minHeap。在插入新 num 的时候，维持 `0 <= leftMaxHeap.size - rightMinHeap.size <= 1`, 即左边比右边不大超过 1 个元素。这样求中位数的时候，如果两 heap size 相同，就返回两者 peek 的平均，否则就返回 leftMaxHeap.peek()。

时间复杂度分析
<br>
* #### Java Code:
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
















