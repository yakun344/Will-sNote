# Unique Paths III

_update Feb 12 2019, 23:39_

[LeetCode](https://leetcode.com/problems/unique-paths-iii/)

On a 2-dimensional grid, there are 4 types of squares:

1 represents the starting square. There is exactly one starting square.  
2 represents the ending square. There is exactly one ending square.  
0 represents empty squares we can walk over.  
-1 represents obstacles that we cannot walk over.

Return the number of 4-directional walks from the starting square to the ending square, that walk over every non-obstacle square exactly once.

**Example 1:**

```text
Input: [[1,0,0,0],[0,0,0,0],[0,0,2,-1]]
Output: 2
Explanation: We have the following two paths: 
1. (0,0),(0,1),(0,2),(0,3),(1,3),(1,2),(1,1),(1,0),(2,0),(2,1),(2,2)
2. (0,0),(1,0),(2,0),(2,1),(1,1),(0,1),(0,2),(0,3),(1,3),(1,2),(2,2)
```

**Example 2:**

```text
Input: [[1,0,0,0],[0,0,0,0],[0,0,0,2]]
Output: 4
Explanation: We have the following four paths: 
1. (0,0),(0,1),(0,2),(0,3),(1,3),(1,2),(1,1),(1,0),(2,0),(2,1),(2,2),(2,3)
2. (0,0),(0,1),(1,1),(1,0),(2,0),(2,1),(2,2),(1,2),(0,2),(0,3),(1,3),(2,3)
3. (0,0),(1,0),(2,0),(2,1),(2,2),(1,2),(1,1),(0,1),(0,2),(0,3),(1,3),(2,3)
4. (0,0),(1,0),(2,0),(2,1),(1,1),(0,1),(0,2),(0,3),(1,3),(1,2),(2,2),(2,3)
```

**Example 3:**

```text
Input: [[0,1],[2,0]]
Output: 0
Explanation: 
There is no path that walks over every empty square exactly once.
Note that the starting and ending square can be anywhere in the grid.
```

**Note:**

`1 <= grid.length * grid[0].length <= 20`

## Basic Idea:

基本思路就是用DFS硬算，考虑所有从start出发到end结尾到路径，用一个matrix标记每个点是否被经过，统计经过所有点的路径个数。需要注意back tracking。

### Java Code:

```java
class Solution {
    private int[] dr = new int[]{0, 1, -1, 0};
    private int[] dc = new int[]{1, 0, 0, -1};

    private int[][] grid;
    private int R, C;
    private int ret;
    private int spaceNum = 0; // 起点和值为0的点的个数

    public int uniquePathsIII(int[][] grid) {
        this.grid = grid;
        R = grid.length;
        C = grid[0].length;
        ret = 0;

        int sr = 0, sc = 0;
        for (int i = 0; i < R; ++i) {
            for (int j = 0; j < C; ++j) {
                if (grid[i][j] == 1) {
                    sr = i;
                    sc = j;
                    spaceNum++;
                } else if (grid[i][j] == 0) {
                    spaceNum++;
                }
            }
        }

        dfs(sr, sc, 0);
        return ret;
    }

    // 保证每次进入dfs的位置一定是valid的，进去之后再标记为visited
    private void dfs(int r, int c, int visitedNum) {
        if (grid[r][c] == 2) {
            if (visitedNum == spaceNum) ret++;
            // System.out.println(spaceNum + " " + visitedNum);
            return;
        }
        grid[r][c] = -1;

        for (int i = 0; i < 4; ++i) {
            int new_r = r + dr[i];
            int new_c = c + dc[i];
            if (isValid(new_r, new_c)) {
                dfs(new_r, new_c, visitedNum + 1);
            }
        }

        grid[r][c] = 0;
    }

    private boolean isValid(int r, int c) {
        return !(r < 0 || r >= R || c < 0 || c >= C || grid[r][c] == -1);
    }
}
```

