# 873. Length of Longest Fibonacci Subsequence

_update Nov 11, 2020_

---
[Leetcode](https://leetcode.com/problems/length-of-longest-fibonacci-subsequence/)

```
A sequence X_1, X_2, ..., X_n is fibonacci-like if:

 1. n >= 3
 2. X_i + X_{i+1} = X_{i+2} for all i + 2 <= n

Given a strictly increasing array A of positive integers forming a sequence, find the length of the longest fibonacci-like subsequence of A.  If one does not exist, return 0.

(Recall that a subsequence is derived from another sequence A by deleting any number of elements (including none) from A, without changing the order of the remaining elements.  For example, [3, 5, 8] is a subsequence of [3, 4, 5, 6, 7, 8].)
```

**Example 1:**
```
Input: [1,2,3,4,5,6,7,8]
Output: 5
Explanation:
The longest subsequence that is fibonacci-like: [1,2,3,5,8].
```

**Example 2:**
```
Input: [1,3,7,11,12,14,18]
Output: 3
Explanation:
The longest subsequence that is fibonacci-like:
[1,11,12], [3,11,14] or [7,11,18].
```

**Note:**
```
1. 3 <= A.length <= 1000
2. 1 <= A[0] < A[1] < ... < A[A.length - 1] <= 10^9
(The time limit has been reduced by 50% for submissions in Java, C, and C++.)
```

## Basic Idea:
我们注意到对于不同的序列，我们只需要其中两个相连的元素就可以确定整个序列，所以我们可以使用DP的思路。使用一个二维DP数组，从前往后，对于每个i，对于每一对`[j,i]，j < i`，我们检查 `A[i]-A[j]`是否存在于给定数组A中，如果存在，假定其index为k，那么此时`dp[k][j]` 应该已经在之前求出（因为这里的右边界j小于i）, 则我们有 `dp[j][i] = dp[k][j] + 1`。 这样我们就可以求出对于所有的右边界i，`[j,i]` 对应的最大长度。

## Java Code:
```java
class Solution {
    public int lenLongestFibSubseq(int[] A) {
        int N = A.length;
        int[][] dp = new int[N][N];
        Map<Integer, Integer> indexMap = new HashMap<>();
        for (int i = 0; i < N; ++i) {
            indexMap.put(A[i], i);
        }
            
        int ret = 0;
        // 对所有在i之前对部分，找到以每对[j,i] 结尾的长度, 记录在
        // dp数组中即为 dp[j][i]
        for (int i = 0; i < N; ++i) {
            for (int j = 0; j < i; ++j) {
                Integer idx = indexMap.get(A[i] - A[j]);
                if (idx != null && idx < j) {
                    dp[j][i] = dp[indexMap.get(A[i] - A[j])][j] + 1;
                    ret = Math.max(dp[j][i], ret);
                }
            }
        }
        return ret < 1 ? 0 : ret + 2;
    }
}
```
