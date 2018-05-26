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

这种方法维持一个`distance[][]` table，从start开始暴力搜索，每次撞墙停止后，更新到达点的distance，再向周围继续搜索。

Time Complexity： 因为搜索覆盖所有从start出发的路径可能，时间复杂度为 `O(m*n*max(m,n))`。
因为共有 `m*n` 个节点，每个节点的搜索可以在不撞墙的情况下搜索 max(m,n) 个格子；

**思路 2：Dijkstra Algorithm**

利用 Dijkstra 单源最短路径的算法，维持一个 `distance[][]` table，从start开始，使用一个 priority queue，每次取当前距离 start 最近的点作为起始点 bfs。撞墙停止之后更新 distance。根据 Dijkstra 算法的定义，每次 poll 出队的节点都是当前已经完成的节点，所以只要遇到 destination 被poll出，就可以结束搜索。

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
上面的实现除了使用 `distance[][]` 之外还使用了一个 `visited[][]` table 来标记每个完成的点，但是事实上我们无需这么做。判断一个节点v是否完成，只需要判断 `distance[v]` 是否小于当前出队节点所记录的距离，如果小于，那么 v 已经完成。

下面的 **python code** 就使用了这样的简化思路：
```python
    class Solution(object):
        def shortestDistance(self, maze, start, destination):
            """
            :type maze: List[List[int]]
            :type start: List[int]
            :type destination: List[int]
            :rtype: int
            """
            def isValid(r, c):
                if r < 0 or r >= R or c < 0 or c >= C:
                    return False
                if maze[r][c] == 1:
                    return False
                return True

            # 先定义变量
            R, C = len(maze), len(maze[0])
            dr = [-1, 1, 0, 0]
            dc = [0, 0, -1, 1]
            distance = [[99999 for i in range(C)] for j in range(R)]
            pq = []

            # Dijkstra
            heapq.heappush(pq, (0, start[0], start[1]))
            distance[start[0]][start[1]] = 0
            while pq:
                weight, r, c = heapq.heappop(pq)
                # 排除已经被更新的在pq中的节点
                if distance[r][c] < weight:
                    continue
                if [r, c] == destination:
                    return distance[r][c]
                for i in range(4):
                    x, y = r, c
                    count = 0
                    while isValid(x + dr[i], y + dc[i]):
                        x += dr[i]
                        y += dc[i]
                        count += 1
                    newDist = count + distance[r][c]
                    # 同时就可以去重
                    if distance[x][y] > newDist:
                        distance[x][y] = newDist
                        heapq.heappush(pq, (newDist, x, y))
            return -1
```

---
_update 2018-05-25 14:03:204_

#### C++ Solution
和之前相比进行了一些简化，由于Dijkstra算法可以保证每次出队的vertex都是当前queue中距离start最近的点，所以当destination出队的时候，就可以直接返回其对应的weight。（这里每个点的weight实际是指其到start的距离）
```cpp
// dijkstra algorithm
class Solution {
    struct Node {
        int r, c, weight;
        Node(int r, int c, int weight) {
            this->r = r;
            this->c = c;
            this->weight = weight;
        }
    };

    bool isValid(const vector<vector<int>>& maze, int r, int c) {
        if (r < 0 || r >= maze.size() || c < 0 || c >= maze[0].size()) return false;
        else if (maze[r][c] == 1) return false;
        else return true;
    }
public:
    int shortestDistance(vector<vector<int>>& maze, vector<int>& start, vector<int>& destination) {
        const int dr[4]{-1, 0, 1, 0};
        const int dc[4]{0, -1, 0, 1};

        auto comp = [](Node a, Node b){ return a.weight > b.weight; };
        priority_queue<Node, vector<Node>, decltype(comp)> pq(comp); // min heap
        pq.push(Node(start[0], start[1], 0));
        unordered_set<string> visited;

        while (! pq.empty()) {
            Node curr = pq.top();
            pq.pop();
            if (curr.r == destination[0] && curr.c == destination[1]) {
                return curr.weight;
            }
            string key = to_string(curr.r) + "," + to_string(curr.c);
            if (visited.count(key)) {
                continue;
            }
            visited.insert(key);
            for (int i = 0; i < 4; ++i) {
                int r = curr.r, c = curr.c;
                int weight = curr.weight;
                while (isValid(maze, r + dr[i], c + dc[i])) {
                    r = r + dr[i];
                    c = c + dc[i];
                    ++weight;
                }
                pq.push(Node(r, c, weight));
            }
        }
        return -1;
    }
};
```
