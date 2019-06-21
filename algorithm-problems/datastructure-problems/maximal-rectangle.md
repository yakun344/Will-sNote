# Maximal Rectangle

_update Sep 25 2018, 21:53_

[LeetCode](https://leetcode.com/problems/maximal-rectangle/description/)

Given a 2D binary matrix filled with `0's` and `1's`, find the largest rectangle containing only `1's` and return its area.

**Example:**

```text
Input:
[
  ["1","0","1","0","0"],
  ["1","0","1","1","1"],
  ["1","1","1","1","1"],
  ["1","0","0","1","0"]
]
Output: 6
```

## Basic Idea:

对于这道题目，我们不可以用 Maximal Square 那道题的dp方法，因为每个顶点对应的可能的长方形有很多种可能。

这道题的方法是先利用dp求每个点上方连续 `'1'` 的个数，然后每一层就相当于一个histogram，接下来对于每层用 maximal rectangle in histogram 的方法求出最大矩形面积，返回全局最大值即可。

**时间复杂度：** 求每个点上方连续1的个数耗时 `O(mn)`，求每行最大面积耗时 `O(n)`，总耗时`O(mn)`;

### Java Code:

```java
class Solution {
    public int maximalRectangle(char[][] matrix) {
        if (matrix.length == 0) return 0;
        // 先算出每个元素上方连续‘1’的个数
        int[][] aux = new int[matrix.length][matrix[0].length];
        for (int c = 0; c < matrix[0].length; ++c) {
            for (int r = 0; r < matrix.length; ++r) {
                int prev = r > 0 ? aux[r - 1][c] : 0;
                aux[r][c] = matrix[r][c] == '1' ? 1 + prev : 0;
            }
        }
        // 利用求largest rectangle in histogram的方法对每一行求最大面积
        int ret = 0;
        for (int r = 0; r < aux.length; ++r) {
            ret = Math.max(ret, getMaxArea(aux[r]));
        }
        return ret;
    }

    private int getMaxArea(int[] input) {
        int[] arr = new int[input.length + 1];
        for (int i = 0; i < input.length; ++i) arr[i] = input[i];
        Deque<Integer> stack = new ArrayDeque<>();
        int ret = 0;
        for (int i = 0; i < arr.length; ++i) {
            if (stack.isEmpty() || arr[stack.peekLast()] <= arr[i]) {
                stack.offerLast(i);
            } else {
                while (! stack.isEmpty() && arr[stack.peekLast()] > arr[i]) {
                    int peek = stack.pollLast();
                    int width = stack.isEmpty() ? i : i - stack.peekLast() - 1;
                    ret = Math.max(arr[peek] * width, ret);
                }
                stack.offerLast(i);
            }
        }
        return ret;
    }
}
```

