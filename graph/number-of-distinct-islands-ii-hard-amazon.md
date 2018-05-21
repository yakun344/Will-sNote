# Number of Distinct Islands II
_update 2018-05-20 20:46:58_

---
[LeetCode](https://leetcode.com/problems/number-of-distinct-islands-ii/description/)

Given a non-empty 2D array grid of 0's and 1's, an island is a group of 1's (representing land) connected 4-directionally (horizontal or vertical.) You may assume all four edges of the grid are surrounded by water.

Count the number of distinct islands. An island is considered to be the same as another if they have the same shape, or have the same shape after rotation (90, 180, or 270 degrees only) or reflection (left/right direction or up/down direction).

**Example 1:**

    11000
    10000
    00001
    00011
    Given the above grid map, return 1. 

**Notice that:**
    
        11
        1
    and
         1
        11
    are considered same island shapes. Because if we make a 180 degrees clockwise rotation 
    on the first island, then two islands will have the same shapes.
    
**Example 2:**

    11100
    10001
    01001
    01110
    Given the above grid map, return 2.

    Here are the two distinct islands:
        111
        1
    and
        1
        1

**Notice that:**

        111
        1
    and
        1
        111
    are considered same island shapes. Because if we flip the first array in the 
    up/down direction, then they have the same shapes.
    
**Note:** The length of each dimension in the given grid does not exceed 50.

<br>

### Basic Idea:
和之前的 **Number of Distinct Islands** 类似，这道题目也可以被分为两部分，一部分是获得所有的island点的集合，二是对每个 island 进行 serilization 生成唯一 hashCode 用于判断是否重复。

而这道题更加难的点在于其需要考虑到包括旋转和翻转在内的多种变换，解决办法就是先得到一个岛所有点的坐标集合，然后分别对其进行各种变换，变换之后进行标准化，使得 left 和 top 都为 0 ，然后对坐标list排序，然后序列化。而最终一个岛的hashCode就是所有变换后生成的string中字典顺序最大或者最小的，这样就能保证同样形状的岛有唯一的hashCode.

* #### Java Code:
```java
class Solution {
    private int[] dr = new int[]{-1, 0, 1, 0};
    private int[] dc = new int[]{0, -1, 0, 1};
    
    public int numDistinctIslands2(int[][] grid) {
        Set<String> shapes = new HashSet<>();
        for (int i = 0; i < grid.length; ++i) {
            for (int j = 0; j < grid[0].length; ++j) {
                if (grid[i][j] == 0) continue;
                List<int[]> shape = new ArrayList<>();
                dfs(grid, i, j, shape);
                String maxHashCode = normalizeHash(shape);
                shapes.add(maxHashCode);
            }
        }
        return shapes.size();
    }
    
    // 对list中的坐标尝试包括原本在内的全部 8 种变换，每次变换之后令 r c 都减去 min_r, min_c 进行标准化， 
    // 然后计算hashCode，返回所有hashCode中的最大值，这样每种固定的形状就有了唯一的hashCode
    private String normalizeHash(List<int[]> shape) {
        String ret = null;
        // 一共有这么多种包括旋转和翻折的变换
        // x y, x -y, -x y, -x -y, y x, y -x, -y x, -y -x
        // 分别生成不同旋转翻折变换后的坐标，标准化，计算hashcode
        for (int i = 0; i < 8; ++i) {
            List<int[]> temp = new ArrayList<>();
            for (int[] coord : shape) {
                int x = i < 2 ? coord[0] : i < 4 ? -coord[0] : i < 6 ? coord[1] : -coord[1];
                int y = i == 0 || i == 2 ? coord[1] : i == 1 || i == 3 ? -coord[1] : i == 4 || i == 6 ? coord[0] : -coord[0];
                temp.add(new int[]{x, y});
            }
            // 
            Collections.sort(temp, (int[] a, int[] b) -> {
                if (a[0] < b[0]) return -1;
                else if (a[0] > b[0]) return 1;
                else return Integer.compare(a[1], b[1]);
            });
            
            // 计算 hashCode
            StringBuilder sb = new StringBuilder();
            int minX = temp.get(0)[0];
            int minY = Integer.MAX_VALUE;
            for (int[] coord : temp) {
                if (minY > coord[1]) minY = coord[1];
            }
            for (int[] coord : temp) {
                sb.append((coord[0] - minX) + "#" + (coord[1] - minY) + "#");
            }
            String t = sb.toString();
            if (ret == null || ret.compareTo(t) < 0) ret = t;
        }
        return ret;
    }
    
    // DFS一个island，把所有点坐标存入list
    private void dfs(int[][] grid, int r, int c, List<int[]> shape) {
        if (r < 0 || r >= grid.length || c < 0 || c >= grid[0].length) return;
        else if (grid[r][c] == 0) return;
        grid[r][c] = 0;
        shape.add(new int[]{r, c});
        for (int i = 0; i < 4; ++i) {
            dfs(grid, r + dr[i], c + dc[i], shape);
        }
    }
}
```




































