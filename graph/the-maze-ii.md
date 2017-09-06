## The Maze II
_update Sep 5,2017  23:58_

---
[LeetCode](https://leetcode.com/problems/the-maze-ii/description/)

There is a ball in a maze with empty spaces and walls. The ball can go through empty spaces by rolling up, down, left or right, but it won't stop rolling until hitting a wall. When the ball stops, it could choose the next direction.

Given the ball's start position, the destination and the maze, find the shortest distance for the ball to stop at the destination. The distance is defined by the number of empty spaces traveled by the ball from the start position (excluded) to the destination (included). If the ball cannot stop at the destination, return -1.

The maze is represented by a binary 2D array. 1 means the wall and 0 means the empty space. You may assume that the borders of the maze are all walls. The start and destination coordinates are represented by row and column indexes.

**Example 1**

    Input 1: a maze represented by a 2D array
             
             0 0 1 0 0
             0 0 0 0 0
             0 0 0 1 0
             1 1 0 1 1
             0 0 0 0 0
             
    Input 2: start coordinate (rowStart, colStart) = (0, 4)
    Input 3: destination coordinate (rowDest, colDest) = (4, 4)
             
    Output: 12
    Explanation: One shortest way is : left -> down -> left -> down -> right -> down -> right.
                          The total distance is 1 + 1 + 3 + 1 + 2 + 2 + 2 = 12.

**Example 2**

    Input 1: a maze represented by a 2D array
             
             0 0 1 0 0
             0 0 0 0 0
             0 0 0 1 0
             1 1 0 1 1
             0 0 0 0 0
             
    Input 2: start coordinate (rowStart, colStart) = (0, 4)
    Input 3: destination coordinate (rowDest, colDest) = (3, 2)
             
    Output: -1
    Explanation: There is no way for the ball to stop at the destination.

**Note:**

-  There is only one ball and one destination in the maze.
-  Both the ball and the destination exist on an empty space, and they will not be at the same position initially.
-  The given maze does not contain border (like the red rectangle in the example pictures), but you could assume the border of the maze are all walls.
-  The maze contains at least 2 empty spaces, and both the width and height of the maze won't exceed 100.

#### Basic Idea:
**思路 1：DFS or BFS 暴力更新最短路径**

这种方法维持一个distance[][] table，从start开始暴力搜索，每次撞墙停止后，更新到达点的distance，再向周围继续搜索。

Time Complexity： 因为搜索覆盖所有从start出发的路径可能，时间复杂度为 `O(m*n*max(m,n))`。
因为共有 `m*n` 个节点，每个节点的搜索可以在不撞墙的情况下搜索 max(m,n) 个格子；

**思路 2：Dijkstra Algorithm**

利用 Dijkstra 单源最短路径的算法，维持一个 distance[][] table，从start开始，使用一个 priority queue，每次取当前距离 start 最近的点作为起始点 bfs。撞墙停止之后更新 distance。根据 Dijkstra 算法的定义，每次 poll 出队的节点都是当前已经完成的节点，所以只要遇到 destination 被poll出，就可以结束搜索。

Time Complexity: `O(m * n * log(mn))`，因为至多有 （mn）个节点和 （mn）条边。

JavaCode:
```java
    // Dijkstra Algorithm with PriorityQueue
    class Solution {
        private class Vertex {
            int r, c, weight;
            public Vertex(int r, int c, int w) {
                this.r = r;
                this.c = c;
                this.weight = w;
            }
        }
        private int R;
        private int C;
        private int[][] MAZE;
        
        public int shortestDistance(int[][] maze, int[] start, int[] destination) {
            // define variables
            R = maze.length;
            C = maze[0].length;
            MAZE = maze;
            int[] dr = new int[]{-1, 1, 0, 0}; // up, down, left, right
            int[] dc = new int[]{0, 0, -1, 1};
            PriorityQueue<Vertex> pq = new PriorityQueue<>(new Comparator<Vertex>(){
                @Override
                public int compare(Vertex v1, Vertex v2) {
                    return v1.weight - v2.weight;
                }
            });
            boolean[][] visited = new boolean[R][C]; 
            int[][] distance = new int[R][C]; // 存放每个可以停止点的当前最短距离
            for (int[] row : distance) {
                Arrays.fill(row, 99999); // 初始distance都设为 INF
            }
            
            // Dijkstra algo, 找start到所有可以停止点的最短距离，如果途中发现dest已经完成，则返回
            pq.offer(new Vertex(start[0], start[1], 0)); // 先把起点放入 pq
            distance[start[0]][start[1]] = 0;
            while (! pq.isEmpty()) {
                Vertex v = pq.poll();
                int r = v.r, c = v.c, weight = v.weight;
                // 这里仍需判断，因为会有被更新过的坐标被重新加入queue，待其被处理之后，其之前的状态仍在pq中
                // 这样判断就会排除这种情况；还有另外一种方法，不用visited，而是判断if distance[r][c] < weight;
                if (visited[r][c]) continue;
                visited[r][c] = true;
                // 若dest已经完成，则返回最短距离
                if (destination[0] == r && destination[1] == c) {
                    return distance[r][c];
                }
                
                // 向四方向搜索，如果在visited中，跳过，否则尝试更新，若更新成功则入队
                for (int i = 0; i < 4; ++i) {
                    int x = r, y = c;
                    int dist = 0;
                    while (isValid(x + dr[i], y + dc[i])) {
                        x += dr[i];
                        y += dc[i];
                        dist++;
                    }
                    if (visited[x][y]) continue;
                    int newDist = distance[r][c] + dist;
                    if (newDist < distance[x][y]) {
                        // 更新成功
                        distance[x][y] = newDist;
                        pq.offer(new Vertex(x, y, newDist));
                    }
                }
            }
            return -1;
        }
        
        // 如果maze[r][c]==0，return true
        private boolean isValid(int r, int c) {
            if (r < 0 || r >= R || c < 0 || c >= C) return false;
            if (MAZE[r][c] == 1) return false;
            return true;
        }
    }
```












