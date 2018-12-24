# Regions Cut By Slashes

_update Dec 23, 19:47_

---
[LeetCode](https://leetcode.com/problems/regions-cut-by-slashes/)

In a `N x N` grid composed of `1 x 1` squares, each `1 x 1` square consists of a `/`, `\`, or blank space.  These characters divide the square into contiguous regions.

(Note that backslash characters are escaped, so a `\` is represented as `"\\"`.)

Return the number of regions.
 

**Example 1:**

    Input:
    [
    " /",
    "/ "
    ]
    Output: 2
    Explanation: The 2x2 grid is as follows:

![](https://assets.leetcode.com/uploads/2018/12/15/1.png)

**Example 2:**

    Input:
    [
    " /",
    "  "
    ]
    Output: 1
    Explanation: The 2x2 grid is as follows:
![](https://assets.leetcode.com/uploads/2018/12/15/2.png)

**Example 3:**

    Input:
    [
    "\\/",
    "/\\"
    ]
    Output: 4
    Explanation: (Recall that because \ characters are escaped, "\\/" refers to \/, and "/\\" refers to /\.)
    The 2x2 grid is as follows:
![](https://assets.leetcode.com/uploads/2018/12/15/3.png)

**Example 4:**

    Input:
    [
    "/\\",
    "\\/"
    ]
    Output: 5
    Explanation: (Recall that because \ characters are escaped, "/\\" refers to /\, and "\\/" refers to \/.)
    The 2x2 grid is as follows:
![](https://assets.leetcode.com/uploads/2018/12/15/4.png)

**Example 5:**

    Input:
    [
    "//",
    "/ "
    ]
    Output: 3
    Explanation: The 2x2 grid is as follows:
![](https://assets.leetcode.com/uploads/2018/12/15/5.png)
 

**Note:**

    1. 1 <= grid.length == grid[0].length <= 30
    2. grid[i][j] is either '/', '\', or ' '.

<br/>

### Basic Idea:
这道题乍一看很新颖，但本质其实是number of islands。我们可以将一个最小的正方形看成左右上下四个三角形组成的。于是我们可以利用union find的解法，对于 ' '，我们union所有的四个三角，对于 '/'，我们分别 union 左上和右下，对于 '\\\\'，我们分别union 左下和右上 。同时因为相邻两个正方形之间的两个三角形一定是共用的，我们也需要做union。

#### Java Code:
```java
class Solution {
    private int[] idxs;
    private int count;
    private int N;
    
    private int left = 0;
    private int top = 1;
    private int right = 2;
    private int down = 3;
    
    private int find(int x) {
        if (idxs[x] == x) return x;
        else {
            idxs[x] = find(idxs[x]);
            return idxs[x];
        }
    }
    
    private void union(int x, int y) {
        int rootx = find(x);
        int rooty = find(y);
        if (rootx == rooty) return;
        idxs[rootx] = rooty;
        count--;
    }
    
    private int genIdx(int r, int c, int pos) {
        return (N * r + c) * 4 + pos;
    }
    
    public int regionsBySlashes(String[] grid) {
        N = grid.length;
        idxs = new int[N * N * 4];
        for (int i = 0; i < idxs.length; ++i) idxs[i] = i;
        count = N * N * 4;
        
        for (int r = 0; r < grid.length; ++r) {
            for (int c = 0; c < grid[0].length(); ++c) {
                char chr = grid[r].charAt(c);
                int topNeighbor = genIdx(r - 1, c, down);
                int leftNeighbor = genIdx(r, c - 1, right);
                // 先处理相邻正方形之间的三角形，这里我们总是和当前正方向左边上上边相邻部分做union
                if (r > 0) union(genIdx(r, c, top), topNeighbor);
                if (c > 0) union(genIdx(r, c, left), leftNeighbor);
                if (chr == ' ') {
                    union(genIdx(r, c, left), genIdx(r, c, top));
                    union(genIdx(r, c, left), genIdx(r, c, right));
                    union(genIdx(r, c, left), genIdx(r, c, down));
                } else if (chr == '/') {
                    union(genIdx(r, c, left), genIdx(r, c, top));
                    union(genIdx(r, c, down), genIdx(r, c, right));
                } else { // case: '\\'
                    union(genIdx(r, c, left), genIdx(r, c, down));
                    union(genIdx(r, c, top), genIdx(r, c, right));
                }
            }
        }
        return count;
    }
}
```
