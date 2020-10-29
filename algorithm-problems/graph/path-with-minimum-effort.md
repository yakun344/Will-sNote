# 1631. Path With Minimum Effort
_update Oct 28, 2020_

---
[Leetcode](https://leetcode.com/problems/path-with-minimum-effort/)

You are a hiker preparing for an upcoming hike. You are given heights, a 2D array of size rows x columns, where heights`[row][col]` represents the height of cell `(row, col)`. You are situated in the top-left cell, `(0, 0)`, and you hope to travel to the bottom-right cell, `(rows-1, columns-1) (i.e., 0-indexed)`. You can move up, down, left, or right, and you wish to find a route that requires the minimum effort.

A route's effort is the **maximum** absolute difference in heights between two consecutive cells of the route.

Return the **minimum effort** required to travel from the top-left cell to the bottom-right cell.

**Example 1:**<br>
![](https://assets.leetcode.com/uploads/2020/10/04/ex1.png)
```
Input: heights = [[1,2,2],[3,8,2],[5,3,5]]
Output: 2
Explanation: The route of [1,3,5,3,5] has a maximum absolute difference of 2 in consecutive cells.
This is better than the route of [1,2,2,2,5], where the maximum absolute difference is 3.
```

**Example 2:**<br>
![](https://assets.leetcode.com/uploads/2020/10/04/ex2.png)
```
Input: heights = [[1,2,3],[3,8,4],[5,3,5]]
Output: 1
Explanation: The route of [1,2,3,4,5] has a maximum absolute difference of 1 in consecutive cells, which is better than route [1,3,5,3,5].
```

**Example 3:**<br>
![](https://assets.leetcode.com/uploads/2020/10/04/ex3.png)
```
Input: heights = [[1,2,1,1,1],[1,2,1,2,1],[1,2,1,2,1],[1,2,1,2,1],[1,1,1,2,1]]
Output: 0
Explanation: This route does not require any effort.
```

**Constraints:**
```c
ows == heights.length
columns == heights[i].length
1 <= rows, columns <= 100
1 <= heights[i][j] <= 1e6
```

## Basic Idea:
要从左上角开始走到右下角，允许上下左右四个方向，而effort的定义是一条路径上穿过的任意相邻格子之间差的绝对值的最大值。看到这里会觉得有些奇怪，会想到使用有些最短路径算法比如Dijkstra，但总觉得这里并不是求一个所有edge weight和的最小值的传统最短路径问题。

1. 仔细想想，Dijkstra是可以做的，其中node的score就定义为走到该node为止所经历的路线上相邻差的最大值。因为dijkstra的特性，我们可以保证对于每次出队的node，它的score是对它来说的最优解，那么当我们见到右下角的node出队时候，就找到了全局的最优解。当然在过程中会出现同一个位置不同score的node先后入队，但我们会选择score更小的node继续，其余相同位置的node则会因为重复被跳过.<br>
整体的时间复杂度不太好分析，但因为每个node最多从上下左右四个方向被入队四次，所以我们可以得到一个时间复杂度的上界，即pq中最多有 `m*n*4` 个元素，最多有 `m*n*4` 次 offer/poll 操作，可以得到上界 `O((m*n*4)*Log(m*n*4))`。
2. 还有另一种使用二分法的做法。我们可以对于最终结果进行二分，而对于给定的结果进行验证，这一步可以使用BFS来做，整体时间复杂度为 `O(m*n*log(1e6))`

## Java Code:
### 1. Dijkstra
```java
class Solution {
    private static class Node {
        int weight, r, c;
        Node(int r, int c, int weight) {
            this.weight = weight;
            this.r = r;
            this.c = c;
        }
    }
    private static int[] dr = new int[]{1, 0, 0, -1};
    private static int[] dc = new int[]{0, 1, -1, 0};

    private int[][] graph;

    // Dijkstra
    public int minimumEffortPath(int[][] heights) {
        this.graph = heights;
        PriorityQueue<Node> pq = new PriorityQueue<>((n1,n2) -> Integer.compare(n1.weight, n2.weight));
        pq.offer(new Node(0, 0, 0));
        Set<String> visited = new HashSet<>();
        while (!pq.isEmpty()) {
            Node curr = pq.poll();
            if (!visited.add(curr.r + "," + curr.c)) {
                // skip duplicated nodes, since these nodes
                // are not optimal results
                continue;
            }
            if (curr.r == graph.length - 1 && curr.c == graph[0].length - 1) {
                return curr.weight;
            }
            for (int i = 0; i < 4; ++i) {
                int newR = curr.r + dr[i];
                int newC = curr.c + dc[i];
                if (!isValid(newR, newC)) {
                    continue;
                }
                int newWeight = Math.max(curr.weight, Math.abs(graph[curr.r][curr.c] - graph[newR][newC]));
                pq.offer(new Node(newR, newC, newWeight));
            }
        }
        // shouldn't reach here, as we can walk to right lower corner anyway
        return -1;
    }

    private boolean isValid(int r, int c) {
        if (r < 0 || r >= graph.length) return false;
        if (c < 0 || c >= graph[0].length) return false;
        return true;
    }
}
```

### 2. Binary Search
```java
class Solution {
    public int minimumEffortPath(int[][] heights) {
        int left = 0, right = 1e6;
        while (left + 1 < right) {
            int mid = (left + right) / 2;
            if (bfsCheck(heights, mid)) {
                right = mid;
            } else {
                left = mid;
            }
        }
        if (bfsCheck(heights, left)) return left;
        else return right;
    }

    private boolean bfsCheck(int[][] graph, int maxDiff) {
        int[] dr = new int[] {0, 1, 0, -1};
        int[] dc = new int[] {1, 0, -1, 0};

        Deque<int[]> queue = new ArrayDeque<>();
        Set<String> visited = new HashSet<>();
        queue.offerFirst(new int[]{0, 0});
        while (!queue.isEmpty()) {
            int[] curr = queue.pollLast();
            int r = curr[0], c = curr[1];
            String key = r + "," + c;
            if (!visited.add(key)) {
                continue;
            }
            if (r == graph.length - 1 && c == graph[0].length - 1) {
                return true;
            }
            for (int i = 0; i < 4; ++i) {
                int newR = r + dr[i];
                int newC = c + dc[i];
                if (!isValid(graph, newR, newC)) {
                    continue;
                }
                int diff = Math.abs(graph[r][c] - graph[newR][newC]);
                if (diff <= maxDiff) {
                    queue.offerFirst(new int[] {newR, newC});
                }
            }
        }
        return false;
    }

    private boolean isValid(int[][] graph, int r, int c) {
        if (r < 0 || r >= graph.length) return false;
        if (c < 0 || c >= graph[0].length) return false;
        return true;
    }
}
```
