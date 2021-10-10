# Largest Rectangle in Histogram

_update Sep 16 2018, 12:39_

[LeetCode](https://leetcode.com/problems/largest-rectangle-in-histogram/description/)

Given n non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.

![](https://leetcode.com/static/images/problemset/histogram.png)

Above is a histogram where width of each bar is 1, given height = \[2,1,5,6,2,3].

![](https://leetcode.com/static/images/problemset/histogram_area.png)

The largest rectangle is shown in the shaded area, which has area = 10 unit.

**Example:**

```
Input: [2,1,5,6,2,3]
Output: 10
```

## Basic Idea:

利用单调栈，找出每个元素左边和右边第一个比它小的元素的index，然后扫描array，对每个元素就可以知道包括它在内的矩形的宽度和高度。宽度是其左右小于它的元素之间的距离，高度就是它自身的高度。输出所有面积中的最大值。

### Java Code：

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        Deque<Integer> stack = new ArrayDeque<>();
        int[] rBounds = new int[heights.length];
        int[] lBounds = new int[heights.length];
        Arrays.fill(rBounds, heights.length);
        Arrays.fill(lBounds, -1);
        // 找每个数字右边第一个比它小的，如果没有则为len
        for (int i = 0; i < heights.length; ++i) {
            while (! stack.isEmpty() && heights[stack.peekLast()] > heights[i]) {
                rBounds[stack.pollLast()] = i;
            }
            stack.offerLast(i);
        }

        // 找每个数字左边第一个比它小的，如果没有则为-1
        for (int i = heights.length - 1; i >= 0; --i) {
            while (! stack.isEmpty() && heights[stack.peekLast()] > heights[i]) {
                lBounds[stack.pollLast()] = i;
            }
            stack.offerLast(i);
        }

        // 计算最大面积
        int ret = 0;
        for (int i = 0; i < heights.length; ++i) {
            int left = lBounds[i];
            int right = rBounds[i];
            ret = Math.max(heights[i] * (right - left - 1), ret);
        }
        return ret;
    }
}
```

_update Sep 25 2018, 15:05_

## Update: 更快的方法，使用一个stack

基本思路和之前的做法一样，仍然是对于每个柱子，找它左右两边第一个比它小的柱子高度，然后计算当前柱子高度所形成的长方形面积，宽度就是左右两边第一个比它低的柱子之间的宽度。但是之前的做法是用两次预处理，生成每个柱子左右两边第一个比它低柱子index的对应关系，而这里只利用一个stack在扫描的过程中动态生成这个信息。

[这里](https://blog.csdn.net/qq508618087/article/details/50336795) 是一个不错的介绍，接下来的内容引用了其中的讲解：

> 思路：这题的一个基本思想是以每一个bar为最低点，向左右遍历直到遇到比他小的bar或边界。这样就能找到一个此bar为最低点的矩形面积。遍历所有的bar之后即可找到最大的矩形面积。但是向左右遍历寻找比他小的bar的时间复杂度是O(n)，在加上遍历一遍所有的bar，总的时间复杂度将为O(n\*n)，是无法通过所有数据的。因此我们需要寻找一种时间复杂度更低的寻找一个bar左右边界的算法。在网上流传了一个设计极其巧妙的方法，借助一个stack可以将时间复杂度降为O(n)。\
> \
>  这种算法的思想是维护一个递增的栈，这个栈保存了元素在数组中的位置。 这样在栈中每一个左边的bar都比本身小，所以左边就天然有界了，也就是左边界就是左边的一个bar。遍历一遍height数组，在将height数组入栈的时候，如果当前元素height\[i]比栈顶元素小，则我们又找到了栈顶元素的右边界。因此我们在此时就可以计算以栈顶元素为最低bar的矩形面积了，因为左右边界我们都已经找到了，而且是在O(1)的时间复杂度内找到的。然后就可以将栈顶元素出栈了。这样每出栈一个元素，即计算以此元素为最低点的矩形面积。当最终栈空的时候我们就计算出了以所有bar为最低点的矩形面积。为保证让所有元素都出栈，我们在height数组最后加一个0，因为一个元素要出栈必须要遇到一个比他小的元素，也就是右边界。\
> \
>  本文来自 小榕流光 的CSDN 博客 ，全文地址请点击：[https://blog.csdn.net/qq508618087/article/details/50336795?utm_source=copy](https://blog.csdn.net/qq508618087/article/details/50336795)

*   **Java Code:**

    ```java
    class Solution {
        public int largestRectangleArea(int[] heights) {
            int[] arr = new int[heights.length + 1];
            for (int i = 0; i < heights.length; ++i) arr[i] = heights[i];
            Deque<Integer> stack = new ArrayDeque<>();
            int maxArea = 0;
            for (int i = 0; i < arr.length; ++i) {
                if (stack.isEmpty() || arr[stack.peekLast()] <= arr[i]) {
                    stack.offerLast(i);
                } else {
                    while (! stack.isEmpty() && arr[stack.peekLast()] > arr[i]) {
                        int peek = stack.pollLast();
                        int width = stack.isEmpty() ? i : i - stack.peekLast() - 1;
                        int currArea = arr[peek] * width;
                        maxArea = Math.max(maxArea, currArea);
                    }
                    stack.offerLast(i);
                }
            }
            return maxArea;
        }
    }
    ```

_update Jan 12, 2019_

## Update: One pass solution 最新实现

利用在输入histogram首尾补0，使得逻辑更加简单，代码也变得更短了。

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int ret = 0;
        Deque<Integer> stack = new ArrayDeque<>();
        int[] arr = new int[heights.length + 2];
        for (int i = 0; i < heights.length; ++i) arr[i + 1] = heights[i];
        for (int i = 0; i < arr.length; ++i) {
            while (! stack.isEmpty() && arr[stack.peekLast()] > arr[i]) {
                int height = arr[stack.pollLast()];
                ret = Math.max(ret, (i - stack.peekLast() - 1) * height);
            }
            stack.offerLast(i);
        }
        return ret;
    }
}
```
