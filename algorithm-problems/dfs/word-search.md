# Word Search

_update 2018-10-22 19:12:17_

[LeetCode](https://leetcode.com/problems/word-search/description/)

Given a 2D board and a word, find if the word exists in the grid.

The word can be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once.

**Example:**

```text
board =
[
    ['A','B','C','E'],
    ['S','F','C','S'],
    ['A','D','E','E']
]

Given word = "ABCCED", return true.
Given word = "SEE", return true.
Given word = "ABCB", return false.
```

## Basic Idea:

基本思路就是在给定的矩阵中dfs，去重的手法可以每次先改动matrix的当前格子，然后再改回去，目的是让dfs过程中的一条路径不会经过同样位置两次，但不同的路径可以经过同样的位置。

* **Java Code:**

  ```java
    class Solution {
        private int R, C;
        public boolean exist(char[][] board, String word) {
            R = board.length;
            C = board[0].length;
            for (int r = 0; r < R; ++r) {
                for (int c = 0; c < C; ++c) {
                    if (board[r][c] == word.charAt(0)) {
                        if (dfs(board, word, 0, r, c)) return true;
                    }
                }
            }
            return false;
        }

        private boolean dfs(char[][] board, String word, int pos, int r, int c) {
            if (pos == word.length() - 1) return true;
            char curr = board[r][c];
            board[r][c] = '*';
            int[] dr = new int[]{0, -1, 0, 1};
            int[] dc = new int[]{-1, 0, 1, 0};
            for (int i = 0; i < 4; ++i) {
                int newR = r + dr[i];
                int newC = c + dc[i];
                if (newR < 0 || newR >= R || newC < 0 || newC >= C 
                    || board[newR][newC] != word.charAt(pos + 1)) continue; // 检查是否valid，同时也考虑了去重
                if (dfs(board, word, pos + 1, newR, newC)) return true;
            }
            board[r][c] = curr;
            return false;
        }
    }
  ```

