## The Maze III
_update Sep 6, 2017  19:59_

---
[LeetCode](https://leetcode.com/problems/the-maze-iii/description/)

There is a ball in a maze with empty spaces and walls. The ball can go through empty spaces by rolling up (u), down (d), left (l) or right (r), but it won't stop rolling until hitting a wall. When the ball stops, it could choose the next direction. There is also a hole in this maze. The ball will drop into the hole if it rolls on to the hole.

Given the ball position, the hole position and the maze, find out how the ball could drop into the hole by moving the shortest distance. The distance is defined by the number of empty spaces traveled by the ball from the start position (excluded) to the hole (included). Output the moving directions by using 'u', 'd', 'l' and 'r'. Since there could be several different shortest ways, you should output the lexicographically smallest way. If the ball cannot reach the hole, output "impossible".

The maze is represented by a binary 2D array. 1 means the wall and 0 means the empty space. You may assume that the borders of the maze are all walls. The ball and the hole coordinates are represented by row and column indexes.

**Example 1**

    Input 1: a maze represented by a 2D array
    
    0 0 0 0 0
    1 1 0 0 1
    0 0 0 0 0
    0 1 0 0 1
    0 1 0 0 0
    
    Input 2: ball coordinate (rowBall, colBall) = (4, 3)
    Input 3: hole coordinate (rowHole, colHole) = (0, 1)

    Output: "lul"
Explanation: There are two shortest ways for the ball to drop into the hole.  
The first way is left -> up -> left, represented by "lul".  
The second way is up -> left, represented by 'ul'.  
Both ways have shortest distance 6, but the first way is lexicographically smaller because 'l' < 'u'. So the output is "lul".

**Example 2**
    
    Input 1: a maze represented by a 2D array
    
    0 0 0 0 0
    1 1 0 0 1
    0 0 0 0 0
    0 1 0 0 1
    0 1 0 0 0
    
    Input 2: ball coordinate (rowBall, colBall) = (4, 3)
    Input 3: hole coordinate (rowHole, colHole) = (3, 0)
    Output: "impossible"
Explanation: The ball cannot reach the hole.
    
**Note:**
-  There is only one ball and one hole in the maze.  
-  Both the ball and hole exist on an empty space, and they will not be at the same position initially.  
-  The given maze does not contain border (like the red rectangle in the example pictures), but you could assume the border of the maze are all walls.   
-  The maze contains at least 2 empty spaces, and the width and the height of the maze won't exceed 30.  
    
#### Basic Idea:  
这道题目困扰了很长时间。第一次见到这道题目，自己只会简单的dfs和bfs，尝试之后发现都不是很好的方法，于是打开Greg的课件开始复习，经过两天学习背景知识之后，开始尝试用 Dijkstra 算法来切入。

一开始有一个疑问，Dijkstra 能否为我们找到所有最短路径？  
>  答案是可以，只要每次更新neighbor的distance时候，当 oldDistance == newDistance 的时候，相应更新pred，这种情况下每个节点的pred应该是一个list，这样最终就可以重建所有路径；

接下来面临的问题是先找到所有最短路径，再按照字典顺序排序，实现起来会否过于复杂？
>  其实我们不必生成所有最短路径，只需要在更新的过程中采用合适的排序策略。在JAVA中，我们可以override CompareTo 函数，实现 1st key: distance, 2nd key: path 的比较规则，然后利用这种规则对neighbor 进行更新。

#### Java Code:  
```java
    class Solution {
        private class Vertex implements Comparable<Vertex>{
            int r, c, distance;
            String path;
            public Vertex(int r, int c, int distance) {
                this.r = r;
                this.c = c;
                this.distance = distance;
                this.path = "";
            }
            public Vertex(Vertex that) {
                this.r = that.r;
                this.c = that.c;
                this.path = that.path;
                this.distance = that.distance;
            }
            
            // 这样写可以实现以distance为 1st key，以path为 2nd key，调用v.compareTo(anotherVertex) 即可
            @Override
            public int compareTo(Vertex v) {
                if (this.distance != v.distance) return this.distance - v.distance;
                else return this.path.compareTo(v.path);
            }
        }
        
        int[][] MAZE;
        int R, C;
        
        public String findShortestWay(int[][] maze, int[] ball, int[] hole) {
            MAZE = maze;
            R = maze.length;
            C = maze[0].length;
            int[] dr = new int[]{-1, 1, 0, 0};
            int[] dc = new int[]{0, 0, -1, 1};
            char[] dir = new char[]{'u', 'd', 'l', 'r'};
            Vertex[][] distance = new Vertex[R][C];
            PriorityQueue<Vertex> pq = new PriorityQueue<>();
            
            // dijkstra
            int r = ball[0], c = ball[1];
            distance[r][c] = new Vertex(r, c, 0);
            pq.offer(new Vertex(r, c, 0));
            while (! pq.isEmpty()) {
                Vertex v = pq.poll();
                if (v.r == hole[0] && v.c == hole[1]) return distance[hole[0]][hole[1]].path;
                // 用以解决更新之后，之前的旧节点仍在pq中的问题
                if (distance[v.r][v.c] != null && v.compareTo(distance[v.r][v.c]) > 0) continue;
                for (int i = 0; i < 4; ++i) {
                    r = v.r; 
                    c = v.c;
                    int count = 0;
                    while (isValid(r + dr[i], c + dc[i])) {
                        r += dr[i];
                        c += dc[i];
                        count++;
                        // 直接break，会把hole的位置和distance加入pq，当它被poll出pq的时候，就可以确定它一定已经结束，
                        // 可以直接返回 distance[hole].path
                        if (r == hole[0] && c == hole[1]) {
                            break;
                        }
                    }
                    // 更新distance table 和 pq 都新建 Vertex 处理，避免 side effect
                    if (count == 0) continue;
                    Vertex temp = new Vertex(r, c, v.distance + count);
                    temp.path = v.path + dir[i];
                    if (distance[r][c] == null || distance[r][c].compareTo(temp) > 0) {
                        distance[r][c] = temp;
                        pq.offer(new Vertex(temp));
                    }
                }
            }
            return "impossible";
        }
        
        private boolean isValid(int r, int c) {
            if (r < 0 || r >= R || c < 0 || c >= C) return false;
            if (MAZE[r][c] == 1) return false;
            return true;
        }
    }
```

















