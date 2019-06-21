# Magic Squares In Grid \(Easy Google\)

_update 2018-05-27 18:20:57_

[LeetCode](https://leetcode.com/problems/magic-squares-in-grid/description/)

A 3 x 3 magic square is a 3 x 3 grid filled with distinct numbers from 1 to 9 such that each row, column, and both diagonals all have the same sum.

Given an grid of integers, how many 3 x 3 "magic square" subgrids are there? \(Each subgrid is contiguous\).

Example 1:

```text
Input: [[4,3,8,4],
        [9,5,1,9],
        [2,7,6,2]]
Output: 1
```

**Explanation:**

```text
The following subgrid is a 3 x 3 magic square:
438
951
276

while this one is not:
384
519
762

In total, there is only one magic square inside the given grid.
```

**Note:**

* `1 <= grid.length <= 10`
* `1 <= grid[0].length <= 10`
* `0 <= grid[i][j] <= 15`

## Basic Idea:

检查每个 3 x 3 的区域，判断其是否满足条件。总时间复杂度为 `O(k*(N^2))`，因为共有 `O(N^2)` 个 3x3 正方形，每个检查需要 k 时间，k 为一个常数。

* **C++ Code:**

  ```cpp
  class Solution {
      bool check(const vector<vector<int>>& grid, int r, int c) {
          int sum = 0;
          for (int i = c; i <= c + 2; ++i) {
              sum += grid[r][i];
          }
          // check rows
          for (int i = r; i <= r + 2; ++i) {
              int rowSum = 0;
              for (int j = c; j <= c + 2; ++j) {
                  if (grid[i][j] > 9 || grid[i][j] < 1) return false;
                  rowSum += grid[i][j];
              }
              if (rowSum != sum) return false;
          }
          // check colums
          for (int j = c; j <= c + 2; ++j) {
              int colSum = 0;
              for (int i = r; i <= r + 2; ++i) {
                  colSum += grid[i][j];
              }
              if (colSum != sum) return false;
          }
          // check one diagonal
          int dSum = 0;
          for (int i = 0; i < 3; ++i) {
              dSum += grid[r + i][c + i];
          }
          if (dSum != sum) return false;
          // check another diagonal
          dSum = 0;
          for (int i = 0; i < 3; ++i) {
              dSum += grid[r + i][c + 2 - i];
          }
          if (dSum != sum) return false;

          return true;
      }
  public:
      int numMagicSquaresInside(vector<vector<int>>& grid) {
          if (grid.size() < 3 || grid[0].size() < 3) return 0;
          int res = 0;
          for (int i = 0; i < grid.size() - 2; ++i) {
              for (int j = 0; j < grid[0].size() - 2; ++j) {
                  res += check(grid, i, j);
              }
          }
          return res;
      }
  };
  ```

