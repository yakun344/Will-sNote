# The Maze

_update Sep 4, 2017 23:52_

[LeetCode](https://leetcode.com/problems/the-maze/description/)

There is a ball in a maze with empty spaces and walls. The ball can go through empty spaces by rolling up, down, left or right, but it won't stop rolling until hitting a wall. When the ball stops, it could choose the next direction.

Given the ball's start position, the destination and the maze, determine whether the ball could stop at the destination.

The maze is represented by a binary 2D array. 1 means the wall and 0 means the empty space. You may assume that the borders of the maze are all walls. The start and destination coordinates are represented by row and column indexes.

**Example 1**

```text
Input 1: a maze represented by a 2D array

0 0 1 0 0
0 0 0 0 0
0 0 0 1 0
1 1 0 1 1
0 0 0 0 0

Input 2: start coordinate (rowStart, colStart) = (0, 4)
Input 3: destination coordinate (rowDest, colDest) = (4, 4)

Output: true
Explanation: One possible way is : left -> down -> left -> down -> right -> down -> right.
```

**Example 2**

```text
Input 1: a maze represented by a 2D array

0 0 1 0 0
0 0 0 0 0
0 0 0 1 0
1 1 0 1 1
0 0 0 0 0

Input 2: start coordinate (rowStart, colStart) = (0, 4)
Input 3: destination coordinate (rowDest, colDest) = (3, 2)

Output: false
Explanation: There is no way for the ball to stop at the destination.
```

**Note:**

* There is only one ball and one destination in the maze.
* Both the ball and the destination exist on an empty space, and they will not be at the same position initially.
* The given maze does not contain border \(like the red rectangle in the example pictures\), but you could assume the border of the maze are all walls.
* The maze contains at least 2 empty spaces, and both the width and height of the maze won't exceed 100.

## Basic Idea:

**简短复述一下题意**：

给定一个由 0 1 构成的迷宫，再给定一个起始坐标start和一个目的地坐标destination。从start出发，可以向上下左右四个方向移动，碰到墙壁才会停止，然后才可以转向。另外，只有停在destination的位置上才行，经过不算。返回true或者false。

关于一些**细节**：

1. 关于搜索中去重，主要保证不停在同一个地方重新搜索就可以了。也就是说，每次在停下之后再将坐标加入visited，并且也是在停下之后再检查当前位置是否在visited中；
2. 因为上面的原因，BFS的时候也需要在停下之后再将其加入queue，poll出之后再判断是否在visited中，若不在，再加入visited；

**思路 1，DFS：**

* 2个参数，初始坐标 start，visited矩阵;
* 以当前start为开始，向除dir外的三个方向探测，走到直到撞墙，然后递归调用，继续搜索；
* 无需关心来路方向，因为无论在当前start同方向继续调用还是折返回到前一个位置，都会遇到visited中的点而被返回；
* 利用方向数组 dr dc 可以把四个方向写进一个for loop，使代码更简洁；

**自我感觉自己这段code写的很不错，值得日后复习。**

Java Code:

```java
    class Solution {
        private int[] dr;
        private int[] dc;
        private int[][] MAZE;
        private int R;
        private int C;
        private int[] dest;
        private boolean res;
        public boolean hasPath(int[][] maze, int[] start, int[] destination) {
            dr = new int[]{-1, 1, 0, 0}; // u d l r
            dc = new int[]{0, 0, -1, 1}; // u d l r
            MAZE = maze;
            R = maze.length;
            C = maze[0].length;
            dest = destination;
            res = false;

            dfs(start, new boolean[R][C]);
            return res;
        }

        private void dfs(int[] start, boolean[][] visited) {
            int r = start[0], c = start[1];
            if (Arrays.equals(start, dest)) {
                res = true;
                return;
            } 
            if (visited[r][c]) return;
            visited[r][c] = true;
            // up down left right
            for (int i = 0; i < 4; ++i) {
                int x = r, y = c;
                while (isValid(new int[]{x + dr[i], y + dc[i]})) {
                    x += dr[i];
                    y += dc[i];
                }
                dfs(new int[]{x, y}, visited);
            }
        }

        // return true if the coord is valid and maze[coord] == 0;
        private boolean isValid(int[] coord) {
            int r = coord[0];
            int c = coord[1];
            if (r < 0 || r >= R || c < 0 || c >= C) return false; 
            if (MAZE[r][c] == 1) return false;
            return true;
        }
    }
```

**思路 2：BFS** 和dfs的做法类似，用停下再入队，出队检查去重的原则。每次poll新坐标之后先检查是否在visited中，如果不在，加入visited中，然后向四个方向搜索，走到撞墙，把最后一个valid的坐标加入queue；

Python Code:

```python
    # BFS Solution;
    class Solution(object):
        def hasPath(self, maze, start, destination):
            """
            :type maze: List[List[int]]
            :type start: List[int]
            :type destination: List[int]
            :rtype: bool
            """
            # return true if input is valid and maze[input]== 0
            def isValid(coord):
                r = coord[0]
                c = coord[1]
                if r < 0 or r >= R or c < 0 or c >= C:
                    return False
                if maze[r][c] == 1:
                    return False
                return True

            # 先定义变量
            R = len(maze)
            C = len(maze[0])
            dr = [-1, 1, 0, 0] # u,d,l,r
            dc = [0, 0, -1, 1]

            queue = collections.deque()
            visited = [[False for i in range(C)] for j in range(R)]
            queue.appendleft(tuple(start))
            # BFS
            while queue:
                curr = queue.pop()
                r, c = curr
                if visited[r][c]: continue
                else: visited[r][c] = True
                for i in range(4):
                    newCoord = [r, c]
                    while isValid((newCoord[0] + dr[i], newCoord[1] + dc[i])):
                        newCoord[0] += dr[i]
                        newCoord[1] += dc[i]
                    if newCoord == destination:
                        return True
                    queue.appendleft(tuple(newCoord))

            return False
```

_udpate 2018-05-24 21:30:14_

## C++ Code

```cpp
class Solution {
    const int dr[4]{0, -1, 0, 1};
    const int dc[4]{-1, 0, 1, 0};
    bool dfs(const vector<vector<int>>& maze, vector<int> start, vector<int>& destination,
             unordered_set<string>& visited) {
        if (visited.count(to_string(start[0]) + "," + to_string(start[1]))) return false;
        else if (start == destination) return true;
        visited.insert(to_string(start[0]) + "," + to_string(start[1]));
        for (int i = 0; i < 4; ++i) {
            int r = start[0], c = start[1];
            while (isValid(maze, r + dr[i], c + dc[i])) {
                r += dr[i];
                c += dc[i];
            }
            if (dfs(maze, vector<int>{r, c}, destination, visited)) return true;
        }
        return false;
    }

    bool isValid(const vector<vector<int>>& maze, int r, int c) {
        if (r < 0 || r >= maze.size() || c < 0 || c >= maze[0].size()) return false;
        else if (maze[r][c] == 1) return false;
        return true;
    }
public:
    bool hasPath(vector<vector<int>>& maze, vector<int>& start, vector<int>& destination) {
        unordered_set<string> visited;
        return dfs(maze, start, destination, visited);
    }
};
```

