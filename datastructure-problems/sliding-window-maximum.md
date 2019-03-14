# Sliding Window Maximum
_update Sep 6 2018, 12:56_

---
[LeetCode](https://leetcode.com/problems/sliding-window-maximum/description/)

Given an array nums, there is a sliding window of size k which is moving from the very left of the array to the very right. You can only see the k numbers in the window. Each time the sliding window moves right by one position. Return the max sliding window.

**Example:**

    Input: nums = [1,3,-1,-3,5,3,6,7], and k = 3
    Output: [3,3,5,5,6,7]

**Explanation:**

    Window position                Max
    ---------------               -----
    [1  3  -1] -3  5  3  6  7       3
     1 [3  -1  -3] 5  3  6  7       3
     1  3 [-1  -3  5] 3  6  7       5
     1  3  -1 [-3  5  3] 6  7       5
     1  3  -1  -3 [5  3  6] 7       6
     1  3  -1  -3  5 [3  6  7]      7

**Note:**

You may assume k is always valid, 1 ≤ k ≤ input array's size for non-empty array.

**Follow up:**  
Could you solve it in linear time?

<br/>

### Basic Idea:
* #### Solution 1: Using PriorityQueue
我们可以维持一个size为k的PQ，随着window的移动增减元素，由于Java自带PQ不支持删除，我们可以使用TreeSet来代替。但是这样做的时间复杂度为 `O(nlogk)`，不是 O(n).

* #### Solution 2: Using Monotonic deque（类似单调栈）
我们可以维持一个deque，其中包含窗口内的元素的index，但其内维持一个从左到右单调递减的顺序，这样就可以保证最左端永远储存当前窗口内max元素的index。  
举例说明：  
对于窗口中元素为 `1 3 -1` 的情况，deque中存储 `3 -1` 两个元素对应的index。当窗口移动使得 1 出去，2 进来：`3 -1 2`，因为 deque中当前最大 3 未移出window，所以不需要移除当前最大，但是我们要检查刚进来的2，使得deque维持从左到右的降序，所以要让 -1 的index移除，再将 2 的index加入右边，所以之后的deque为 `3 2` 对应的index；  

* ##### Java Code:
```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        Deque<Integer> deque = new ArrayDeque<>();
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < nums.length; ++i) {
            if (! deque.isEmpty() && i - deque.peekFirst() + 1 > k) {
                // 当前最大移出窗口
                deque.pollFirst();
            }
            while (! deque.isEmpty() && nums[deque.peekLast()] < nums[i]) {
                // 弹出右边比当前数字小的数的index，维持递减性质
                deque.pollLast();
            }
            deque.offerLast(i);
            if (i >= k - 1) {
                res.add(deque.peekFirst());
            }
        }
        return res.stream().mapToInt(a->nums[a]).toArray();
    }
}
```
---
_update Mar 14 2019, 15:07_

### Update: Monotonic Queue 可以独立实现
参考花花的讲解: [https://www.youtube.com/watch?v=2SXqBsTR6a8](https://www.youtube.com/watch?v=2SXqBsTR6a8), 我们可以把单调队列单独写成一个util类，支持三种操作：

1. `offer`: 从右端加入一个元素，保证queue从左到右降序
2. `pop`: 将最左端元素移除
3. `getMax`: 返回最左端元素，当前max

这样一个类的n次操作平均时间为O(1)每次。


