# Number of Islands II (Hard Google)
_update 2018-05-20 14:05:02_

---
[LeetCode](https://leetcode.com/problems/number-of-islands-ii/description/)

A 2d grid map of `m` rows and `n` columns is initially filled with water. We may perform an addLand operation which turns the water at position (row, col) into a land. Given a list of positions to operate, count the number of islands after each addLand operation. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

**Example:**

    Input: m = 3, n = 3, positions = [[0,0], [0,1], [1,2], [2,1]]
    Output: [1,1,2,3]

**Explanation:**
    
    Initially, the 2d grid grid is filled with water. (Assume 0 represents water and 1 represents land).
    
    0 0 0
    0 0 0
    0 0 0
    Operation #1: addLand(0, 0) turns the water at grid[0][0] into a land.
    
    1 0 0
    0 0 0   Number of islands = 1
    0 0 0
    Operation #2: addLand(0, 1) turns the water at grid[0][1] into a land.
    
    1 1 0
    0 0 0   Number of islands = 1
    0 0 0
    Operation #3: addLand(1, 2) turns the water at grid[1][2] into a land.
    
    1 1 0
    0 0 1   Number of islands = 2
    0 0 0
    Operation #4: addLand(2, 1) turns the water at grid[2][1] into a land.
    
    1 1 0
    0 0 1   Number of islands = 3
    0 1 0

**Follow up:**
Can you do it in time complexity `O(k log mn)`, where k is the length of the positions?

<br>

### Basic Idea:
每次操作在图中增加一个点，要求记录每次操作完成之后独立 island 的个数。可以理解为每次操作后求图中所有联通量的个数，自然就想到了并查集（Union Find or Disjoint Set）。

**定义并查集：**   
* 将并查集表示为长度为 `m*n` 的数组，并且把每个初始值设为 -1 表示该点为空。则矩阵中的点`[r,c]`就对应了 ids 数组中的 `ids[r*n+c]`；

**并查集操作：**  
* 增加点的时候，先令 `ids[k] = k; count++;`，然后检查上下左右四个点所对应的并查集中是否有不为 -1 的值，若有则做 union 操作。
* 每次操作完成之后，将 `count` 的值加入 res list 即可。

**时间复杂度：**  
这样做的话时间复杂度为 `O(m*n + k)` 其中k为operation的个数，因为并查集的操作amortize之后为 O(1)，前面的 `m*n` 为建 `ids` 数组的时间，如果用 `HashMap` 优化，则可以将时间复杂度优化为 `O(k)`；

#### Java Code:
```java
class Solution {
    int[] ids;
    int[] ranks;
    int count = 0;
    void makeSet(int size) {
        ids = new int[size];
        Arrays.fill(ids, -1);
        ranks = new int[size];
    }
    
    int find(int id) {
        if (ids[id] == id) return id;
        while (ids[id] != id) {
            id = ids[id];
        }
        return id;
    }
    
    void union(int x, int y) {
        int rootx = find(x);
        int rooty = find(y);
        if (rootx == rooty) return;
        if (ranks[rootx] < ranks[rooty]) ids[rootx] = rooty;
        else if (ranks[rooty] < ranks[rootx]) ids[rooty] = rootx;
        else {
            ids[rootx] = rooty;
            ranks[rooty]++;
        }
        count--;
    }
    
    public List<Integer> numIslands2(int m, int n, int[][] positions) {
        int[] dr = new int[]{-1, 0, 1, 0};
        int[] dc = new int[]{0, -1, 0, 1};
        List<Integer> res = new ArrayList<>();
        
        makeSet(m * n);
        for (int[] coord : positions) {
            int r = coord[0], c = coord[1];
            int k = r * n + c;
            if (ids[k] != -1) continue;
            ids[k] = k;
            count++;
            for (int i = 0; i < 4; ++i) {
                int nr = r + dr[i];
                int nc = c + dc[i];
                if (nr < 0 || nr >= m || nc < 0 || nc >= n) continue;
                int nk = nr * n + nc;
                if (ids[nk] != -1) union(nk, k);
            }
            res.add(count);
        }
        return res;
    }
}
```
































