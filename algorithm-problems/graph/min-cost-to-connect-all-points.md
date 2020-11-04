1584. Min Cost to Connect All Points
_update Oct 28, 2020_

---
[Leetcode](https://leetcode.com/problems/min-cost-to-connect-all-points/)

You are given an array points representing integer coordinates of some points on a 2D-plane, where points`[i] = [xi, yi]`.

The cost of connecting two points `[xi, yi]` and `[xj, yj]` is the manhattan distance between them: `|xi - xj| + |yi - yj|`, where `|val|` denotes the absolute value of val.

Return the minimum cost to make all points connected. All points are connected if there is exactly one simple path between any two points.

**Example 1:**

![](https://assets.leetcode.com/uploads/2020/08/26/d.png)
```
Input: points = [[0,0],[2,2],[3,10],[5,2],[7,0]]
Output: 20
Explanation:
```

![](https://assets.leetcode.com/uploads/2020/08/26/c.png)
```
We can connect the points as shown above to get the minimum cost of 20.
Notice that there is a unique path between every pair of points.

```

**Example 2:**
```
Input: points = [[3,12],[-2,5],[-4,1]]
Output: 18
```

**Example 3:**
```
Input: points = [[0,0],[1,1],[1,0],[-1,1]]
Output: 4
```

**Example 4:**
```
Input: points = [[-1000000,-1000000],[1000000,1000000]]
Output: 4000000
```

**Example 5:**
```
Input: points = [[0,0]]
Output: 0
```

**Constraints:**
```
1. 1 <= points.length <= 1000
2. -106 <= xi, yi <= 106
3. All pairs (xi, yi) are distinct.
```

## Basic Idea:
这道题目是比较典型的MST（最小生成树）的问题，解决MST问题常见的比较好的做法是Kruskal算法，可以做到 `O(ElogE)` 的时间复杂度。简单来说，Kruskal 就是将所有的边按照weight从小到大排序，从小到大检查每条边相连的点是否已经被加入MST，如果没有，则将其加入。对于这部操作可以使用并查集（Union Find）。所以总体时间复杂度就是对于边排序的时候最慢，即 `O(ElogE)`.

当然也可以使用Prim算法来做，但Prim需要从一个点出发，然后每次relax一个边直到找到所有的点，如果不做优化时间复杂度是非常大的，对于这道题来说是 `O(N^2)`.

## Java Code:
```java
class Solution {
    int[] ids;
    int count = 0;
        
    private int find(int x) {
        if (ids[x] != x) {
            ids[x] = find(ids[x]);
        }
        return ids[x];
    }
    
    private void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        ids[rootX] = ids[rootY];
        count--;
    }
    
    private void makeSet(int size) {
        ids = new int[size];
        count = size;
        for (int i = 0; i < size; ++i) {
            ids[i] = i;
        }
    }
    
    public int minCostConnectPoints(int[][] points) {
        if (points.length == 1) {
            return 0;
        }
        int N = points.length;
        int numEdge = N * (N - 1) / 2;
        makeSet(N);
        
        int[][] edges = new int[numEdge][3];
        int edgeId = 0;
        for (int i = 0; i < N; ++i) {
            for (int j = i + 1; j < N; ++j) {
                edges[edgeId][0] 
                    = Math.abs(points[i][0] - points[j][0]) + Math.abs(points[i][1] - points[j][1]);
                edges[edgeId][1] = i;
                edges[edgeId][2] = j;
                edgeId++;
            }
        }
        
        Arrays.sort(edges, (a, b) -> Integer.compare(a[0], b[0]));
        int ret = 0;
        for (int i = 0; i < edges.length; ++i) {
            int u = edges[i][1];
            int v = edges[i][2];
            int weight = edges[i][0];
            if (find(u) != find(v)) {
                union(u, v);
                ret += weight;
            }
            if (count == 1) return ret;
        }
        return -1;
    }
}
```
