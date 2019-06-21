# Grid Illumination

_update Mar 5 2019, 19:08_

[LeetCode](https://leetcode.com/problems/grid-illumination/)

On a N x N grid of cells, each cell `(x, y)` with `0 <= x < N` and `0 <= y < N` has a lamp.

Initially, some number of lamps are on. `lamps[i]` tells us the location of the i-th lamp that is on. Each lamp that is on illuminates every square on its x-axis, y-axis, and both diagonals \(similar to a Queen in chess\).

For the i-th query `queries[i] = (x, y)`, the answer to the query is 1 if the cell `(x, y)` is illuminated, else 0.

After each query `(x, y)` \[in the order given by queries\], we turn off any lamps that are at cell `(x, y)` or are adjacent 8-directionally \(ie., share a corner or edge with cell `(x, y)`.\)

Return an array of answers. Each value `answer[i]` should be equal to the answer of the i-th query `queries[i]`.

**Example 1:**

```text
Input: N = 5, lamps = [[0,0],[4,4]], queries = [[1,1],[1,0]]
Output: [1,0]

Explanation: 

Before performing the first query we have both lamps [0,0] and [4,4] on.
The grid representing which cells are lit looks like this, where [0,0] is the top left corner, and [4,4] is the bottom right corner:
1 1 1 1 1
1 1 0 0 1
1 0 1 0 1
1 0 0 1 1
1 1 1 1 1
Then the query at [1, 1] returns 1 because the cell is lit.  After this query, the lamp at [0, 0] turns off, and the grid now looks like this:
1 0 0 0 1
0 1 0 0 1
0 0 1 0 1
0 0 0 1 1
1 1 1 1 1
Before performing the second query we have only the lamp [4,4] on.  Now the query at [1,0] returns 0, because the cell is no longer lit.
```

**Note:**

1. `1 <= N <= 10^9`
2. `0 <= lamps.length <= 20000`
3. `0 <= queries.length <= 20000`
4. `lamps[i].length == queries[i].length == 2`

## Basic Idea:

题意是说给定一个pair数组 lamps 和一个pair数组 queries，以及一个整数 N。N 表示整个grid的边长，lamps表示亮灯的坐标。灯笼可以照亮横竖以及两个对角线。query也是坐标，返回该点是否亮，但query完成之后需要关掉以它为中心的九个格子的灯笼。

需要注意的是这道题目所给的 N 的范围很大，所以我们不可以真的建这么大的grid，而是需要用map来存row, col 以及两个对角线。然后先根据lamps更新这四个map，再在处理quries的时候将lamp去掉，同时更新map。

**技巧：**

> 对于对角线，我们也可以用一个点点坐标来唯一标示：对于点`(x,y)`，其所在点从左下到右上的对角线可以表示为 `x+y`, 从左上到右下到对角线可以表示为 `x-y`。

### Python Code：

```python
from collections import defaultdict

class Solution:
    def gridIllumination(self, N: int, lamps: List[List[int]], queries: List[List[int]]) -> List[int]:
        # store lamps in a Map<int, Set<int>>
        lampMap = defaultdict(set)
        for r,c in lamps:
            lampMap[r].add(c)

        rowMap, colMap, dMap1, dMap2 = defaultdict(int), defaultdict(int), defaultdict(int), defaultdict(int)
        # update lamp maps
        for r,c in lamps:
            rowMap[r] += 1
            colMap[c] += 1
            dMap1[r + c] += 1
            dMap2[r - c] += 1

        ret = [0] * len(queries)
        for k,(r,c) in enumerate(queries):
            if rowMap[r] > 0 or colMap[c] > 0 or dMap1[r + c] > 0 or dMap2[r - c] > 0:
                ret[k] = 1
            for i in range(-1, 2):
                for j in range(-1, 2):
                    nr, nc = r + i, c + j
                    if nc in lampMap[nr]:
                        lampMap[nr].remove(nc)
                        # 熄灯 nr,nc
                        rowMap[nr] -= 1
                        colMap[nc] -= 1
                        dMap1[nr + nc] -= 1
                        dMap2[nr - nc] -= 1

        return ret
```

### Java Code:

```java
class Solution {
    public int[] gridIllumination(int N, int[][] lamps, int[][] queries) {
        Map<Integer, Integer> rowMap = new HashMap<>();
        Map<Integer, Integer> colMap = new HashMap<>();
        Map<Integer, Integer> d1Map = new HashMap<>(); // 左下-右上 对角线
        Map<Integer, Integer> d2Map = new HashMap<>(); // 左上-右下 对角线
        Map<Integer, Set<Integer>> lampMap = new HashMap<>();
        for (int[] lamp : lamps) {
            int row = lamp[0], col = lamp[1];
            rowMap.put(row, rowMap.getOrDefault(row, 0) + 1);
            colMap.put(col, colMap.getOrDefault(col, 0) + 1);
            d1Map.put(row + col, colMap.getOrDefault(row + col, 0) + 1);
            d2Map.put(row - col, colMap.getOrDefault(row - col, 0) + 1);
            lampMap.putIfAbsent(row, new HashSet<>());
            lampMap.get(row).add(col);
        }

        List<Integer> res = new ArrayList<>();
        for (int[] query : queries) {
            int r = query[0], c = query[1];
            // turn off lamps, 8 points
            for (int i = -1; i <= 1; ++i) {
                for (int j = -1; j <= 1; ++j) {
                    if (i == 0 && j == 0) continue;
                    int x = r + i, y = c + j;
                    if (x < 0 || x >= N || y < 0 || y >= N) continue;
                    Set<Integer> lampCol = lampMap.get(x);
                    if (lampCol != null && lampCol.contains(y)) {
                        lampCol.remove(y);
                        // 关灯，行.列.两个对角线
                        rowMap.put(x, rowMap.getOrDefault(x, 0) - 1);
                        colMap.put(y, colMap.getOrDefault(y, 0) - 1);
                        d1Map.put(x + y, d1Map.getOrDefault(x + y, 0) - 1);
                        d2Map.put(x - y, d2Map.getOrDefault(x - y, 0) - 1);
                    }
                }
            }
            // 检查行，列，两个对角线，是否被照亮
            if (rowMap.getOrDefault(r, 0) > 0
                || colMap.getOrDefault(c, 0) > 0
                || d1Map.getOrDefault(r + c, 0) > 0
                || d2Map.getOrDefault(r - c, 0) > 0) {
                res.add(1);
            } else {
                res.add(0);
            }
        }
        return res.stream().mapToInt(a->a).toArray();
    }
}
```

