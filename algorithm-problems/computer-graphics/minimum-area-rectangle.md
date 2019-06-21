# Minimum Area Rectangle

_update Dec 30 18:03, 2018_

[LeetCode](https://leetcode.com/problems/minimum-area-rectangle/)

Given a set of points in the xy-plane, determine the minimum area of a rectangle formed from these points, with sides parallel to the x and y axes.

If there isn't any rectangle, return 0.

**Example 1:**

```text
Input: [[1,1],[1,3],[3,1],[3,3],[2,2]]
Output: 4
```

**Example 2:**

```text
Input: [[1,1],[1,3],[3,1],[3,3],[4,1],[4,3]]
Output: 2
```

**Note:**

1. 1 &lt;= points.length &lt;= 500
2. 0 &lt;= points\[i\]\[0\] &lt;= 40000
3. 0 &lt;= points\[i\]\[1\] &lt;= 40000
4. All points are distinct.

## Basic Idea:

因为只考虑平行于坐标轴的矩形，这种情况下一条对角线就可以确定一个矩形。我们只需要枚举所有对角线，检查另外两点是否存在，然后就可以计算矩形面积。时间复杂度 `O(n^2)`，因为共有 `n(n-1)/2` 个连线。

* **Java Code:**

  ```java
    class Solution {
        public int minAreaRect(int[][] points) {
            Set<Integer> set = new HashSet<>();
            for (int[] point : points) {
                set.add(point[0] * 40001 + point[1]);
            }
            int minArea = Integer.MAX_VALUE;
            // 枚举所有连线，因为只考虑平行于坐标轴的矩形，一条对角线就可以确定一个矩形
            for (int i = 0; i < points.length; ++i) {
                for (int j = i + 1; j < points.length; ++j) {
                    int[] p1 = points[i], p2 = points[j];
                    if (p1[0] == p2[0] || p1[1] == p2[1]) continue;
                    int[] p3 = new int[]{p1[0], p2[1]};
                    int[] p4 = new int[]{p2[0], p1[1]};
                    if (set.contains(p3[0] * 40001 + p3[1]) && set.contains(p4[0] * 40001 + p4[1])) {
                        // 说明找到一个矩形，计算面积
                        minArea = Math.min(minArea, Math.abs(p1[0] - p2[0]) * Math.abs(p1[1] - p2[1]));
                    }
                }
            }
            return minArea < Integer.MAX_VALUE ? minArea : 0;
        }
    }
  ```

