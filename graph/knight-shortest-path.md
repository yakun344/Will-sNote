## Knight Shortest Path
_update Jul 19, 2017 10:50_

---
[liintcode](http://www.lintcode.com/en/problem/knight-shortest-path/)

Given a knight in a chessboard (a binary matrix with 0 as empty and 1 as barrier) with a source position, find the shortest path to a destination position, return the length of the route. 
Return -1 if knight can not reached.

** Notice**

source and destination must be empty.
Knight can not enter the barrier.

Have you met this question in a real interview? Yes
Clarification
If the knight is at (x, y), he can get to the following positions in one step:

     (x + 1, y + 2)
     (x + 1, y - 2)
     (x - 1, y + 2)
     (x - 1, y - 2)
     (x + 2, y + 1)
     (x + 2, y - 1)
     (x - 2, y + 1)
     (x - 2, y - 1)
**Example**

     [[0,0,0],
      [0,0,0],
      [0,0,0]]
     source = [2, 0] destination = [2, 2] return 2
     
     [[0,1,0],
      [0,0,0],
      [0,0,0]]
     source = [2, 0] destination = [2, 2] return 6
     
     [[0,1,0],
      [0,0,1],
      [0,0,0]]
     source = [2, 0] destination = [2, 2] return -1
     
#### Basic Idea:
基本思想就是做分层BFS，每层就是每步。值得注意的是对dx dy坐标变换数组的进一步应用。

加速的解法是从source和destination两个点同时开始bfs，这就是传说中的**双向广度优先搜索算法**。其中有一些细节需要注意。
1.  双向BFS由于搜索深度减半，可以获得相当于普通BFS开根号级别的时间复杂度。
2.  要注意双向BFS是双向逐层搜索，所以是每次搜索一个队列中的全部节点。
3.  需要注意奇偶不同的边界条件（参考下面的实现）。
4.  当一方队列为空时，如果仍未找到，则说明搜索失败了。

#### Java Implementation：
*  普通BFS
```java
    public int shortestPath(boolean[][] grid, Point source, Point destination) {
        if (grid == null || grid.length == 0) return -1;
        int R = grid.length;
        int C = grid[0].length;
        int[] dx = new int[]{1, 1, -1, -1, 2, 2, -2, -2};
        int[] dy = new int[]{2, -2, 2, -2, 1, -1, 1, -1};
        if (grid[source.x][source.y] || grid[destination.x][destination.y]) {
            return -1;
        }
        int ret = 0;
        Deque<Point> queue = new LinkedList<>();
        queue.addFirst(source);
        // bfs过的点标记为1，即不会重复
        while (! queue.isEmpty()) {
            int size = queue.size();
            ret++;
            for (int j = 0; j < size; ++j) {
                Point point = queue.removeLast();
                for (int i = 0; i < 8; ++i) {
                    int m = point.x + dx[i];
                    int n = point.y + dy[i];
                    if (m < 0 || m >= grid.length || n < 0 || n >= grid[0].length) {
                        continue;
                    }
                    if (m == destination.x && n == destination.y) return ret; 
                    if (! grid[m][n]) {
                        grid[m][n] = true;
                        queue.addFirst(new Point(m, n));
                    }
                }
            }
        }
        return -1;
    }
```

*  双向BFS
```java
    // 双向BFS，sqrt（V + E）时间
    public int shortestPath(boolean[][] grid, Point source, Point destination) {
        if (grid == null || grid.length == 0) return -1;
        int R = grid.length;
        int C = grid[0].length;
        int[] dx = new int[]{1, 1, -1, -1, 2, 2, -2, -2};
        int[] dy = new int[]{2, -2, 2, -2, 1, -1, 1, -1};
        if (grid[source.x][source.y] || grid[destination.x][destination.y]) {
            return -1;
        } 
        // 把grid改为int[][]，把两边bfs visited的点分别标记为 2 和 3。
        int[][] graph = new int[R][C];
        for (int i = 0; i < R; ++i) {
            for (int j = 0; j < C; ++j) {
                graph[i][j] = grid[i][j] ? 1 : 0;
            }
        }
        // 开始双向bfs
        Deque<Point> q1 = new LinkedList<>();
        Deque<Point> q2 = new LinkedList<>();
        q1.addFirst(source);
        q2.addFirst(destination);
        graph[source.x][source.y] = 2;
        graph[destination.x][destination.y] = 3;
        int step = 0;
            // 若一方队列为空，则fail
        while (! q1.isEmpty() && ! q2.isEmpty()) {
           
            int smallStep = 0; // ！！！这里很关键！！！！
            
            // bfs1, 进行一层
            int size1 = q1.size();
            smallStep++;
            for (int i = 0; i < size1; ++i) {
                Point p = q1.removeLast();
                for (int j = 0; j < 8; ++j) {
                    int m = p.x + dx[j];
                    int n = p.y + dy[j];
                    if (m < 0 || m >= R || n < 0 || n >= C) continue;
                    if (graph[m][n] == 3) {
                        // 此时相遇
                        return step * 2 + smallStep;
                    }
                    if (graph[m][n] == 0) {
                        graph[m][n] = 2;
                        q1.addFirst(new Point(m, n));
                    }
                }
            }
            // bfs2，进行一层
            int size2 = q2.size();
            smallStep++;
            for (int i = 0; i < size2; ++i) {
                Point p = q2.removeLast();
                for (int j = 0; j < 8; ++j) {
                    int m = p.x + dx[j];
                    int n = p.y + dy[j];
                    if (m < 0 || m >= R || n < 0 || n >= C) continue;
                    if (graph[m][n] == 2) {
                        // 此时相遇
                        return step * 2 + smallStep;
                    }
                    if (graph[m][n] == 0) {
                        graph[m][n] = 3;
                        q2.addFirst(new Point(m, n));
                    }
                }
            }
            step++;
        }
        return -1;
    }
```