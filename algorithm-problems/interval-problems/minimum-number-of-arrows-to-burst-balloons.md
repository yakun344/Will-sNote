# Minimum Number of Arrows to Burst Balloons

_update Aug 6,2017 15:36_

[LeetCode](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/description/)

There are a number of spherical balloons spread in two-dimensional space. For each balloon, provided input is the start and end coordinates of the horizontal diameter. Since it's horizontal, y-coordinates don't matter and hence the x-coordinates of start and end of the diameter suffice. Start is always smaller than end. There will be at most 104 balloons.

An arrow can be shot up exactly vertically from different points along the x-axis. A balloon with xstart and xend bursts by an arrow shot at x if xstart ≤ x ≤ xend. There is no limit to the number of arrows that can be shot. An arrow once shot keeps travelling up infinitely. The problem is to find the minimum number of arrows that must be shot to burst all balloons.

**Example:**

```text
Input:
[[10,16], [2,8], [1,6], [7,12]]

Output:
2
```

**Explanation:** One way is to shoot one arrow for example at x = 6 \(bursting the balloons \[2,8\] and \[1,6\]\) and another arrow at x = 11 \(bursting the other two balloons\).

## Basic Idea:

解决类似的 Interval Overlapping 问题的套路：

1. Maintain 一个 minEnd，用来追踪当前 arrow 的位置，以及一个 count 追踪最小 arrow 数量;
2. 对于每段 interval，

   如果和 minEnd 没有交集：interval.left &gt; minEnd, 则 count++，minEnd = interval.right;

   如果有交集：则更新 minEnd = min{ minEnd, interval.right }

## Java Code：

```java
    public class Solution {
        public int findMinArrowShots(int[][] points) {
            if (points == null || points.length == 0 || points[0].length == 0) return 0;
            // 先排序
            Arrays.sort(points, (p1, p2) -> p1[0] - p2[0]);
            int count = 1;
            int minEnd = points[0][1];
            for (int i = 1; i < points.length; ++i) {
                int[] interval = points[i];
                if (interval[0] <= minEnd) {
                    // 如果 curr interval 和 minEnd 有交集
                    minEnd = Math.min(minEnd, interval[1]);
                } else {
                    count++;
                    minEnd = interval[1];
                }
            }
            return count;
        }
    }
```

