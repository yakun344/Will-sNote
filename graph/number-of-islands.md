## Number of Islands
_update Jul 18, 2017 17:26_

---
[lintcode](http://www.lintcode.com/en/problem/number-of-islands/)

Given a boolean 2D matrix, 0 is represented as the sea, 1 is represented as the island. If two 1 is adjacent, we consider them in the same island. We only consider up/down/left/right adjacent.

Find the number of islands.

**Example**
    Given graph:
    
    [
      [1, 1, 0, 0, 0],
      [0, 1, 0, 0, 1],
      [0, 0, 0, 1, 1],
      [0, 0, 0, 0, 0],
      [0, 0, 0, 0, 1]
    ]
    return 3.
    
#### Basic Idea:
首先我们可以想到的就是使用一个bfs helper function，对每个原图中为1的点bfs，然后用一个visited set记录是否已经visited。每次成功进入bfs，则ret += 1. 由此思路，我们可以进一步考虑，如果我们不使用额外的visited set，只在原图中改动，其实也可以达到目的，我们只需在每次bfs的时候，将遇到的点全部改为0，这样每次进入bfs的时候即说明当前的1没有被visited，ret++。

**具体的**我们注意到在处理对上下左右四个方向bfs的时候，为了代码concise，我们使用了`rdir 和 cdir`两个`int[]`，其实是列出了r和c两个坐标每次的偏移量，
```
    {-1, 0, 1, 0}
    {0, -1, 0, 1}
```
其实就表示了下左上右四个方向的相邻坐标点。九章算法中把这个内容叫做**坐标变换数组**。

#### Java Code:
```java
    public class Solution {
        /**
         * @param grid a boolean 2D matrix
         * @return an integer
         */
         
        // 对每个值为true的点进行bfs，bfs的过程中遇到的点都标记为false，每次重新开始
        // 进入bfs，则ret += 1
        public int numIslands(boolean[][] grid) {
            if (grid == null || grid.length == 0) return 0;
            List<int[]> nodes = new ArrayList<>();
            for (int i = 0; i < grid.length; ++i) {
                for (int j = 0; j < grid[0].length; ++j) {
                    if (grid[i][j]) nodes.add(new int[]{i, j});
                }
            }
            int ret = 0;
            for (int[] cord : nodes) {
                int r = cord[0], c = cord[1];
                if (grid[r][c]) {
                    bfs(grid, new int[]{r, c});
                    ret++;
                }
            }
            return ret;
        }
        private void bfs(boolean[][] grid, int[] cord) {
            int r = cord[0], c = cord[1];
            int R = grid.length, C = grid[0].length;
            // 把bfs入口的点设为false
            grid[r][c] = false;
            Deque<int[]> queue = new LinkedList<>();
            queue.addFirst(cord);
            // 下面两个分别代表下左上右四个点的坐标偏移量
            int[] rdir = new int[]{-1, 0, 1, 0};
            int[] cdir = new int[]{0, -1, 0, 1};
            while (! queue.isEmpty()) {
                cord = queue.removeLast();
                r = cord[0];
                c = cord[1];
                for (int i = 0; i < 4; ++i) {
                    int m = r + rdir[i];
                    int n = c + cdir[i];
                    if (m >= 0 && m < R && n >= 0 && n < C) {
                        if (! grid[m][n]) continue;
                        grid[m][n] = false;
                        queue.addFirst(new int[]{m, n});
                    }
                }
            }
        }
    }
```