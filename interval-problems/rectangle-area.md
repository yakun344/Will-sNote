# Rectangle Area
_update 2018-10-21 02:17:29_

---
[LeetCode](https://leetcode.com/problems/rectangle-area/submissions/)

Find the total area covered by two rectilinear rectangles in a 2D plane.

Each rectangle is defined by its bottom left corner and top right corner as shown in the figure.

![](https://leetcode.com/static/images/problemset/rectangle_area.png)

**Example:**

    Input: A = -3, B = 0, C = 3, D = 4, E = 0, F = -1, G = 9, H = 2
    Output: 45

**Note:**

Assume that the total area is never beyond the maximum possible value of int.

<br/>

### Basic Idea:
我们可以根据图来理解。求两个矩形组成图形的总面积就是两矩形面积之和减去重叠部分面积。所以关键就是如何求重叠部分的面积，可以如图观察发现，底边长等于两右边界中最小值减去两左边界中最大值。而高则等于两上边界最小值减去两下边界最大值。

#### Java Code:
```java
class Solution {
    public int computeArea(int A, int B, int C, int D, int E, int F, int G, int H) {
        int area1 = (C - A) * (D - B);
        int area2 = (G - E) * (H - F);
        int left = Math.max(A, E);
        int right = Math.min(C, G);
        int top = Math.min(D, H);
        int bottom = Math.max(B, F);
        int overLap = (right - left) * (top - bottom);
        if (left < right && top > bottom) return area1 + area2 - overLap;
        else return area1 + area2;
    }
}
```
