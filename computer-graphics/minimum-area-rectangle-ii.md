# Minimum Area Rectangle II
_update Dec 27, 22:35 2018_

---
[LeetCode](https://leetcode.com/problems/minimum-area-rectangle-ii/)

Given a set of points in the xy-plane, determine the minimum area of any rectangle formed from these points, with sides not necessarily parallel to the x and y axes.

If there isn't any rectangle, return 0.

 
**Example 1:**

![](https://assets.leetcode.com/uploads/2018/12/21/1a.png)

    Input: [[1,2],[2,1],[1,0],[0,1]]
    Output: 2.00000
    Explanation: The minimum area rectangle occurs at [1,2],[2,1],[1,0],[0,1], with an area of 2.

**Example 2:**

![](https://assets.leetcode.com/uploads/2018/12/22/2.png)

    Input: [[0,1],[2,1],[1,1],[1,0],[2,0]]
    Output: 1.00000
    Explanation: The minimum area rectangle occurs at [1,0],[1,1],[2,1],[2,0], with an area of 1.

**Example 3:**

![](https://assets.leetcode.com/uploads/2018/12/22/3.png)

    Input: [[0,3],[1,2],[3,1],[1,3],[2,1]]
    Output: 0
    Explanation: There is no possible rectangle to form from these points.

**Example 4:**

![](https://assets.leetcode.com/uploads/2018/12/21/4c.png)

    Input: [[3,1],[1,1],[0,1],[2,1],[3,3],[3,2],[0,2],[2,3]]
    Output: 2.00000
    Explanation: The minimum area rectangle occurs at [2,1],[2,3],[3,3],[3,1], with an area of 2. 

**Note:**

    1. 1 <= points.length <= 50
    2. 0 <= points[i][0] <= 40000
    3. 0 <= points[i][1] <= 40000
    4. All points are distinct.
    5. Answers within 10^-5 of the actual value will be accepted as correct.

<br/>

### Basic Idea:
* #### 对角线长度相等且互相平分
  我们可以利用矩形的性质：对角线平分且长度相等的四边形是矩形。于是我们可以先用 `O(n^2)` 的时间枚举所有的两点连线，以他们的长度和中点坐标为key存入map。然后对map中的每一组互相平分长度相等的连线，两重for loop计算每个矩形的面积。

  时间复杂度是 `O(n^2)`，因为所有连线最多 `n(n-1)/2`，而我们知道不同组的长度相同且平分的对角线之间不会出现这种关系，所以amortized来说，共有最多 `O(n^2)` 个矩形，而后面的两重for loop实际上是检查了所有的矩形，所以总时间复杂度仍然是 `O(n^2)`。

  ```java
    class Solution {
        public double minAreaFreeRect(int[][] points) {
            // 计算每条连线的中点和长度的平方，以此为key将连线的两个端点存入map
            Map<String, List<int[]>> map = new HashMap<>();
            for (int i = 0; i < points.length; ++i) {
                int[] p1 = points[i];
                for (int j = i + 1; j < points.length; ++j) {
                    int[] p2 = points[j];
                    double mid_x = (p1[0] + p2[0]) / 2.0;
                    double mid_y = (p1[1] + p2[1]) / 2.0;
                    double lenSq = Math.pow(p1[0] - p2[0], 2) + Math.pow(p1[1] - p2[1], 2);
                    String key = lenSq + "," + mid_x + "," + mid_y;
                    if (! map.containsKey(key)) map.put(key, new ArrayList<>());
                    map.get(key).add(new int[]{i, j}); // 将当前连线的两端点的index存入map
                }
            }
            double minArea = 2 * (double)Integer.MAX_VALUE;
            // 所有key相等的连线可以两两一对作为长方形的对角线
            Iterator<Map.Entry<String, List<int[]>>> it = map.entrySet().iterator();
            while (it.hasNext()) {
                List<int[]> lst = it.next().getValue();
                if (lst.size() < 2) continue;
                for (int i = 0; i < lst.size(); ++i) {
                    int[] idxs = lst.get(i);
                    int[] p1 = points[idxs[0]];
                    int[] p2 = points[idxs[1]];
                    for (int j = i + 1; j < lst.size(); ++j) {
                        idxs = lst.get(j);
                        int[] p3 = points[idxs[0]];
                        int[] p4 = points[idxs[1]];
                        double diagLenSq = Math.pow(p1[0] - p2[0], 2) + Math.pow(p1[1] - p2[1], 2);
                        double edgeLenSq = Math.pow(p1[0] - p3[0], 2) + Math.pow(p1[1] - p3[1], 2);
                        minArea = Math.min(minArea, Math.sqrt(edgeLenSq) * Math.sqrt(diagLenSq - edgeLenSq));
                    }
                }
            }
            return minArea > Integer.MAX_VALUE ? 0 : minArea;
        }
    }
  ```

* #### `O(n^3)` solution
  列出这个解法主要是为了复习一些向量的操作手法。我们可以 `O(n^3)` 时间枚举所有的三个点的组合，利用向量的性质判断它们能否组成矩形的互相垂直的两条边，然后利用向量的性质求出第四个点的坐标，判断其是否存在。然后就可以计算面积了。

  ```java
    class Solution {
        public double minAreaFreeRect(int[][] points) {
            // store all points in map first
            Map<Integer, Set<Integer>> map = new HashMap<>();
            for (int i = 0; i < points.length; ++i) {
                map.putIfAbsent(points[i][0], new HashSet<>());
                map.get(points[i][0]).add(points[i][1]);
            }
            double minArea = 2 * (double)Integer.MAX_VALUE;
            // 三重for loop考虑所有三点组合，如果可以形成矩形，检查第四个点是否存在，
            // 如果存在，计算面积
            for (int i = 0; i < points.length; ++i) {
                for (int j = i + 1; j < points.length; ++j) {
                    for (int k = j + 1; k < points.length; ++k) {
                        int[] p1 = points[i], p2 = points[j], p3 = points[k];
                        // 分别以p123作为直角点, 通过swap保证p2为直角点
                        if (isVertical(p1, p2, p3)) {
                            ;
                        } else if (isVertical(p1, p3, p2)) {
                            int[] t = p2;
                            p2 = p3;
                            p3 = t;
                        } else if (isVertical(p2, p1, p3)) {
                            int[] t = p2;
                            p2 = p1;
                            p1 = t;
                        } else {
                            continue;
                        }
                        int[] p4 = getP4(p1, p2, p3);
                        if (map.getOrDefault(p4[0], new HashSet<>()).contains(p4[1])) {
                            minArea = Math.min(minArea, getArea(p1, p2, p3));
                        }
                    }
                }
            }
            return minArea > Integer.MAX_VALUE ? 0 : minArea;
        }
        
        // 检查角p1p2p3是否是直角
        // 利用向量点乘为0
        boolean isVertical(int[] p1, int[] p2, int[] p3) {
            int[] v12 = new int[]{p1[0] - p2[0], p1[1] - p2[1]};
            int[] v23 = new int[]{p2[0] - p3[0], p2[1] - p3[1]};
            return v12[0] * v23[0] + v12[1] * v23[1] == 0;
        }
        
        // 计算由p1p2,p2p3两条直角边构成矩形的第四个顶点
        // 利用矩形平行边向量相等
        int[] getP4(int[] p1, int[] p2, int[] p3) {
            int x4 = p1[0] - p2[0] + p3[0];
            int y4 = p1[1] - p2[1] + p3[1];
            return new int[]{x4, y4};
        }
        
        // 计算由p2为直角点形成的矩形面积
        double getArea(int[] p1, int[] p2, int[] p3) {
            double len12 = Math.sqrt(Math.pow(p1[0] - p2[0], 2) + Math.pow(p1[1] - p2[1], 2));
            double len23 = Math.sqrt(Math.pow(p2[0] - p3[0], 2) + Math.pow(p2[1] - p3[1], 2));
            return len12 * len23;
        }
    }
  ```
