# Number of Distinct Islands \(Medium Amazon\)

_update 2018-05-20 20:31:51_

[LeetCode](https://leetcode.com/problems/number-of-distinct-islands/description/)

Given a non-empty 2D array grid of 0's and 1's, an island is a group of 1's \(representing land\) connected 4-directionally \(horizontal or vertical.\) You may assume all four edges of the grid are surrounded by water.

Count the number of distinct islands. An island is considered to be the same as another if and only if one island can be translated \(and not rotated or reflected\) to equal the other.

**Example 1:**

```text
11000
11000
00011
00011
Given the above grid map, return 1.
```

**Example 2:**

```text
11011
10000
00001
11011
Given the above grid map, return 3.
```

**Notice that:**

```text
        11
        1
        and
         1
        11
  are considered different island shapes, because we do not consider 
reflection / rotation.
```

**Note:**

* The length of each dimension in the given grid does not exceed 50.

## Basic Idea:

这道题可以被分解为两部分，一部分是找到所有独立的island，另一部分则是判断当前island之前是否出现过，即判断两island是否相同。 找到所有的island这部分可以通过简单的 DFS 来解决，而第二部分则要依靠类似 serialize 的方法将 island 存为 string，然后利用 HashSet 来去重。

我们可以先标准化每个 island，将其左上角第一个node的坐标转化为 `[0, 0]`。

* **Java Code:**

  ```java
    class Solution {
        int[] dr = new int[]{0, -1, 0, 1};
        int[] dc = new int[]{-1, 0, 1, 0};
        public int numDistinctIslands(int[][] grid) {
            Set<String> shapes = new HashSet<>();
            for (int r = 0; r < grid.length; ++r) {
                for (int c = 0; c < grid[0].length; ++c) {
                    if (grid[r][c] == 0) continue;
                    StringBuilder shape = new StringBuilder();
                    dfs(grid, r, c, r, c, shape);
                    shapes.add(shape.toString());
                }
            }
            return shapes.size();
        }

        // dfs 函数中，传入左上角的坐标 r0,c0，利用这个坐标对整个岛进行标准化
        private void dfs(int[][] grid, int r0, int c0, int r, int c, StringBuilder shape) {
            if (r < 0 || r >= grid.length || c < 0 || c >= grid[0].length) return;
            else if (grid[r][c] == 0) return;
            grid[r][c] = 0;
            shape.append((r - r0) + "#" + (c - c0) + "#"); // serialize步骤, 先标准化
            for (int i = 0; i < 4; ++i) {
                dfs(grid, r0, c0, r + dr[i], c + dc[i], shape);
            }
        }
    }
  ```

* **C++ Code:**

  与前面 Java 的解法不同，这里没有再 dfs 中直接生成 string，而是将所有 coord 存入一个 vector，然后再排序，生成 string；

  ```cpp
    class Solution {
        int dr[4] = {1, 0, -1, 0};
        int dc[4] = {0, 1, 0, -1};

        void dfs(vector<vector<int>>& grid, int r0, int c0, int r, int c, vector<vector<int>>& shape) {
            if (r < 0 || r >= grid.size() || c < 0 || c >= grid[0].size()) return;
            else if (grid[r][c] == 0) return;
            grid[r][c] = 0;
            shape.push_back({r - r0, c - c0});
            for (int i = 0; i < 4; ++i) {
                dfs(grid, r0, c0, r + dr[i], c + dc[i], shape);
            }
        }
    public:
        int numDistinctIslands(vector<vector<int>>& grid) {
            unordered_set<string> shapes;
            for (int r = 0; r < grid.size(); ++r) {
                for (int c = 0; c < grid[0].size(); ++c) {
                    if (grid[r][c] == 0) continue;
                    vector<vector<int>> shape;
                    dfs(grid, r, c, r, c, shape);

                    // 对坐标list排序保证顺序，然后生成string
                    sort(shape.begin(), shape.end(), [](vector<int> v1, vector<int> v2) -> bool {
                        if (v1[0] != v2[0]) return v1[0] < v2[0];
                        else return v1[1] < v2[1];
                    });
                    string hashCode;
                    for (vector<int>& coord : shape) {
                        hashCode.append(to_string(coord[0]) + "#" + to_string(coord[1]) + "#");
                    }
                    shapes.insert(hashCode);
                }
            }
            return shapes.size();
        }
    };
  ```

