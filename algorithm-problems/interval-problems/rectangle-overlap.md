# Rectangle Overlap

_update 2018-10-21 02:09:49_

[LeetCode](https://leetcode.com/problems/rectangle-overlap/description/)

A rectangle is represented as a list `[x1, y1, x2, y2]`, where `(x1, y1)` are the coordinates of its bottom-left corner, and `(x2, y2)` are the coordinates of its top-right corner.

Two rectangles overlap if the area of their intersection is positive. To be clear, two rectangles that only touch at the corner or edges do not overlap.

Given two \(axis-aligned\) rectangles, return whether they overlap.

**Example 1:**

```text
Input: rec1 = [0,0,2,2], rec2 = [1,1,3,3]
Output: true
```

**Example 2:**

```text
Input: rec1 = [0,0,1,1], rec2 = [1,0,2,1]
Output: false
```

**Notes:**

1. Both rectangles rec1 and rec2 are lists of 4 integers.
2. All coordinates in rectangles will be between `-10^9` and `10^9`.

## Basic Idea:

基本思想就是：两个矩形两个左边界坐标的最大值如果小于两个右边界的最小值，说明x轴上投影有overlap。同样，如果两个下边界坐标的最大值比两个上边界的最小值小，说明在y轴方向上有overlap。如果两者都满足，则说明两矩形有overlap。

### Java Code：

```java
class Solution {
    public boolean isRectangleOverlap(int[] rec1, int[] rec2) {
        return (Math.max(rec1[0], rec2[0]) < Math.min(rec1[2], rec2[2]) 
               && Math.max(rec1[1], rec2[1]) < Math.min(rec1[3], rec2[3]));
    }
}
```

