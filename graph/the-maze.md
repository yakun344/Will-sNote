## The Maze
_update Sep 4, 2017  23:52_

---
[LeetCode](https://leetcode.com/problems/the-maze/description/)

There is a ball in a maze with empty spaces and walls. The ball can go through empty spaces by rolling up, down, left or right, but it won't stop rolling until hitting a wall. When the ball stops, it could choose the next direction.

Given the ball's start position, the destination and the maze, determine whether the ball could stop at the destination.

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
    
    Output: true
    Explanation: One possible way is : left -> down -> left -> down -> right -> down -> right.

**Example 2**

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

**Note:**
-  There is only one ball and one destination in the maze.
-  Both the ball and the destination exist on an empty space, and they will not be at the same position initially.
-  The given maze does not contain border (like the red rectangle in the example pictures), but you could assume the border of the maze are all walls.
-  The maze contains at least 2 empty spaces, and both the width and height of the maze won't exceed 100.


#### Basic Idea:
**简短复述一下题意**：

给定一个由 0 1 构成的迷宫，再给定一个起始坐标start和一个目的地坐标destination。从start出发，可以向上下左右四个方向移动，碰到墙壁才会停止，然后才可以转向。另外，只有停在destination的位置上才行，经过不算。返回true或者false。

**思路 1，DFS：**











