## N-Queens
_update Jan 16,2018  21:32_

---
[LeetCode](https://leetcode.com/problems/n-queens/description/)

The n-queens puzzle is the problem of placing n queens on an n×n chessboard such that no two queens attack each other.

![](/assets/8-queens.png)

Given an integer n, return all distinct solutions to the n-queens puzzle.

Each solution contains a distinct board configuration of the n-queens' placement, where `'Q'` and `'.'` both indicate a queen and an empty space respectively.

**For example,**

     There exist two distinct solutions to the 4-queens puzzle:

     [
      [".Q..",  // Solution 1
       "...Q",
       "Q...",
       "..Q."],

      ["..Q.",  // Solution 2
       "Q...",
       "...Q",
       ".Q.."]
     ]

<br>

### Basic Idea:
&emsp; 首先想到这是一道 dfs 的题目，因为要生成所有的解的集合。大致思路如下：  
&emsp; 首先明确queen的性质，她可以杀与其同在一条直线上的其他棋子（横竖斜），而我们要在 `N*N` 大小的棋盘中放入 N 个 queen，所以我们一定是每个 row 放一个 queen。然后，具体地，我们对每一个 row，考虑其上每个 col 位置放 queen 的话会不会影响之前放置好的第 `0 to row-1` row 上的queen。如果当前 `(row, col)` 可以，就继续向 `row + 1` 继续 dfs。

**时间空间复杂度分析：**  
&emsp; 整个算法如果是 brute force 解，求出所有可能的组合再判断是否 valid，需要 `O(N^N)` 的时间复杂度，因为在每一个 row，都需要考虑 N 个 col 的情况。但是因为我们可以每次判断当前 col 能否放置 queen，相当于在搜索的过程中进行了剪枝，所以我们至少可以将时间复杂度优化为 `O(N!)` (即不考虑之前放置过queen的col，所以每次可选col数量递减1)； 因为我们还剪掉了斜线上的情况，所以最后的时间复杂度应该是优于 `O(N!)`；  
&emsp; 空间的话，recursion 的 call stack 最大深度为 N，如果不考虑储存和处理解集的空间，整个空间复杂度应该是 `O(N)`;

#### Java Code:
由于题目要求的返回值是一个 `List<List<String>>` ，而对于每一个棋盘，我们认为 `List<Integer>` 更加容易进行判断当前col放queen是否会影响到之前，所以我们的dfs只会将结果存储在一个 `List<List<Integer>>` 中，然后另外用一个 `drawChessboard()` 来将解转化为所需要的return value;

```java
class Solution {
    public List<List<String>> solveNQueens(int n) {
        List<List<Integer>> lst = new ArrayList<>();
        dfs(lst, 0, new ArrayList<>(), n);
        List<List<String>> res = drawChessboard(lst, n);
        return res;
    }

    // 对于每个row，考虑每个col能否放queen，如果可以，继续向下一个row dfs
    // path 中存放的是每个 row 放置queen的 col 的值 (0 to n-1)
    private void dfs(List<List<Integer>> res, int row, List<Integer> path, int n) {
        if (row == n) {
            res.add(new ArrayList<>(path));
            return;
        }
        // consider each col
        for (int col = 0; col < n; ++col) {
            if (isValid(path, row, col, n)) {
                path.add(col);
                dfs(res, row + 1, path, n);
                path.remove(path.size() - 1);
            }
        }
    }

    // 判断当前 row，col 位置放 queen 的话会不会影响到之前的 row
    private boolean isValid(List<Integer> path, int row, int col, int n) {
        int k = 1;
        while (k <= row) {
            int queen = path.get(row - k);
            if (queen == col || queen == col - k || queen == col + k) return false;
            k++;
        }
        return true;
    }

    // 将用数字表示每行queen位置的所有解转化为string的二维list形式
    private List<List<String>> drawChessboard(List<List<Integer>> lst, int n) {
        List<List<String>> ret = new ArrayList<>();
        for (List<Integer> intList : lst) {
            List<String> stringList = new ArrayList<>();
            for (int queenPos : intList) {
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < n; ++i) {
                    if (i != queenPos) sb.append(".");
                    else sb.append("Q");
                }
                stringList.add(sb.toString());
            }
            ret.add(stringList);
        }
        return ret;
    }
}
```

---
_update 2018-07-29 17:11:37_

#### Update, 更简洁的写法
这里的写法更简单，也可以把 `boolean[n][n]` 换成一个 `int[n]`，存放没row的queen的col.

```java
class Solution {
    public List<List<String>> solveNQueens(int n) {
        List<List<String>> res = new ArrayList<>();
        dfs(n, 0, new boolean[n][n], res);
        return res;
    }

    private void dfs(int n, int row, boolean[][] board, List<List<String>> res) {
        if (row == n) {
            List<String> currBoard = new ArrayList<>();
            for (boolean[] r : board) {
                StringBuilder sb = new StringBuilder();
                for (boolean val : r) {
                    if (val) sb.append('Q');
                    else sb.append('.');
                }
                currBoard.add(sb.toString());
            }
            res.add(currBoard);
            return ;
        }
        for (int col = 0; col < n; ++col) {
            if (isValid(board, row, col)) {
                board[row][col] = true;
                dfs(n, row + 1, board, res);
                board[row][col] = false;
            }
        }
    }

    private boolean isValid(boolean[][] board, int r, int c) {
        if (r < 0 || r >= board.length || c < 0 || c >= board[0].length) return false;
        for (int i = 1; r - i >= 0; ++i) {
            if (c - i >= 0 && board[r - i][c - i]) return false;
            if (c + i < board[0].length && board[r - i][c + i]) return false;
            if (board[r - i][c]) return false;
        }
        return true;
    }
}
```
