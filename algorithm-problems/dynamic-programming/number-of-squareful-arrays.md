# Number of Squareful Arrays

_update Feb 19 2019, 0:41_

[LeetCode](https://leetcode.com/problems/number-of-squareful-arrays/)

Given an array A of non-negative integers, the array is squareful if for every pair of adjacent elements, their sum is a perfect square.

Return the number of permutations of A that are squareful. Two permutations A1 and A2 differ if and only if there is some index i such that `A1[i] != A2[i]`.

**Example 1:**

```text
Input: [1,17,8]
Output: 2
Explanation: 
[1,8,17] and [17,8,1] are the valid permutations.
```

**Example 2:**

```text
Input: [2,2,2]
Output: 1
```

**Note:**

1. `1 <= A.length <= 12`
2. `0 <= A[i] <= 1e9`

## Basic Idea:

### Solution 1: DFS + Pruning

我们可以生成所有可能的permutation，在过程中不断检查新确认的数字和其之前的数字是否满足要求。如果不考虑pruning，时间复杂度为 `O(N!)`，剪枝之后复杂度显著降低，比DP更快。

* **Java Code:**

  ```java
    class Solution {
        public int numSquarefulPerms(int[] A) {
            return dfs(A, 0);
        }

        private int dfs(int[] A, int pos) {
            if (pos == A.length) {
                return 1;
            }
            int ret = 0;
            // 用于去重，不用重复数字替换pos
            Set<Integer> used = new HashSet<>();
            for (int i = pos; i < A.length; ++i) {
                if (!used.add(A[i]) && i > pos) continue;
                if (pos > 0 && !isValid(A[i], A[pos - 1])) continue; // pruning
                swap(A, pos, i);
                ret += dfs(A, pos + 1);
                swap(A, pos, i);
            }
            return ret;
        }

        private void swap(int[] A, int i, int j) {
            int t = A[i];
            A[i] = A[j];
            A[j] = t;
        }

        private boolean isValid(int a, int b) {
            int sum = a + b;
            return (int)Math.pow((int)Math.sqrt(sum), 2) == sum;
        }
    }
  ```

  **Solution 2: DP, Hamitonian Path**

  花花酱的视频讲解：[https://www.youtube.com/watch?v=ISnktonx7rY](https://www.youtube.com/watch?v=ISnktonx7rY) .

这里的实现比较复杂，因为涉及到的去重的操作比较复杂。

