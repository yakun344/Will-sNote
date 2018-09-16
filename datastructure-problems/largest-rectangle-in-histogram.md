# Largest Rectangle in Histogram
_update Sep 16 2018, 12:39_

---
[LeetCode](https://leetcode.com/problems/largest-rectangle-in-histogram/description/)

Given n non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.

![](https://leetcode.com/static/images/problemset/histogram.png)

Above is a histogram where width of each bar is 1, given height = [2,1,5,6,2,3].

![](https://leetcode.com/static/images/problemset/histogram_area.png)

The largest rectangle is shown in the shaded area, which has area = 10 unit.



**Example:**

    Input: [2,1,5,6,2,3]
    Output: 10

### Basic Idea:
利用单调栈，找出每个元素左边和右边第一个比它小的元素的index，然后扫描array，对每个元素就可以知道包括它在内的矩形的宽度和高度。宽度是其左右小于它的元素之间的距离，高度就是它自身的高度。输出所有面积中的最大值。

#### Java Code：
```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        Deque<Integer> stack = new ArrayDeque<>();
        int[] rightSmaller = new int[heights.length];
        int[] leftSmaller = new int[heights.length];
        Arrays.fill(rightSmaller, heights.length);
        Arrays.fill(leftSmaller, -1);
        //  先找每个数右边第一个比它小的数的index, 若没有则存 -1
        for (int i = 0; i < heights.length; ++i) {
            if (stack.isEmpty() || heights[stack.peekLast()] <= heights[i]) {
                stack.offerLast(i);
            } else {
                while (! stack.isEmpty() && heights[stack.peekLast()] > heights[i]) {
                    rightSmaller[stack.pollLast()] = i;
                }
                stack.offerLast(i);
            }
        }
        stack.clear();
        // 再找每个数左边第一个比他小的数的index, 没有则存 heights.length
        for (int i = heights.length - 1; i >= 0; --i) {
            if (stack.isEmpty() || heights[stack.peekLast()] <= heights[i]) {
                stack.offerLast(i);
            } else {
                while (! stack.isEmpty() && heights[stack.peekLast()] > heights[i]) {
                    leftSmaller[stack.pollLast()] = i;
                }
                stack.offerLast(i);
            }
        }
        for (int i = 0; i < leftSmaller.length; ++i) {
            System.out.print(leftSmaller[i] + " ");
        }
        System.out.println();
        for (int i = 0; i < leftSmaller.length; ++i) {
            System.out.print(rightSmaller[i] + " ");
        }
        // 检查每个数字，根据其左右比它小的数字的index确定面积，返回最大值
        int ret = 0;
        for (int i = 0; i < heights.length; ++i) {
            int left = leftSmaller[i];
            int right = rightSmaller[i];
            int area = 0;
            area = heights[i] * (right - left - 1);

            ret = Math.max(ret, area);
        }
        return ret;
    }
}
```
