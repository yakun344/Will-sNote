# Delete Columns to Make Sorted III

_update Dec 25, 21:13_

[LeetCode](https://leetcode.com/problems/delete-columns-to-make-sorted-iii/)

We are given an array A of N lowercase letter strings, all of the same length.

Now, we may choose any set of deletion indices, and for each string, we delete all the characters in those indices.

For example, if we have an array `A = ["babca","bbazb"]` and deletion indices `{0, 1, 4}`, then the final array after deletions is `["bc","az"]`.

Suppose we chose a set of deletion indices D such that after deletions, the final array has every element \(row\) in lexicographic order.

For clarity, `A[0]` is in lexicographic order \(ie. `A[0][0] <= A[0][1] <= ... <= A[0][A[0]`.length - 1\]\), `A[1]` is in lexicographic order \(ie. `A[1][0] <= A[1][1] <= ... <= A[1][A[1]`.length - 1\]\), and so on.

Return the minimum possible value of D.length.

**Example 1:**

```text
Input: ["babca","bbazb"]
Output: 3
Explanation: After deleting columns 0, 1, and 4, the final array is A = ["bc", "az"].
Both these rows are individually in lexicographic order (ie. A[0][0] <= A[0][1] and A[1][0] <= A[1][1]).
Note that A[0] > A[1] - the array A isn't necessarily in lexicographic order.
```

**Example 2:**

```text
Input: ["edcba"]
Output: 4
Explanation: If we delete less than 4 columns, the only row won't be lexicographically sorted.
```

**Example 3:**

```text
Input: ["ghi","def","abc"]
Output: 0
Explanation: All rows are already lexicographically sorted.
```

**Note:**

1. 1 &lt;= A.length &lt;= 100
2. 1 &lt;= A\[i\].length &lt;= 100

## Basic Idea:

起初拿到这道题的时候确实想到了联系LIS的DP解法，但当时只想到了每个String分别处理，也就是分别求出每个string的LIS之后再做打算。但这样是很复杂的。后来看了 lee215 的讲解之后恍然大悟。

我们可以将每一列看作一个整体，这样就可以直接应用LIS的解法了。不同的是普通的LIS中我们需要每次判断当前char是否比之前的第i个char大或相等，而现在我们要判断当前这列是否每个char都比之前的第i列对应行的char大或相等。

最终我们的`dp[i]`中存放了包括第 i 列作为结尾的最长递增序列大长度，而所求大解就是总长度减去全局最大递增序列长度。

时间复杂度为 `O(M^2 * N)`, 其中M为String的长度，N为String的个数。

```java
class Solution {
    public int minDeletionSize(String[] A) {
        int[] dp = new int[A[0].length()];
        // 从左到右一次计算如果以第c列为最右(included)，最长递增序列的长度
        for (int c = 0; c < A[0].length(); ++c) {
            dp[c] = 1; // 最小为1
        OUTER:
            // 检查c列之前的每一列，看是否能和c列组成更长的递增序列
            for (int i = 0; i < c; ++i) {
                // 检查r行第i列是否每个字母都比r行第c列的小或相等
                for (int r = 0; r < A.length; ++r) {
                    if (A[r].charAt(c) < A[r].charAt(i)) {
                        continue OUTER;
                    }
                }
                dp[c] = Math.max(dp[i] + 1, dp[c]); 
            }
        }
        int maxLen = 0;
        for (int i = 0; i < dp.length; ++i) maxLen = Math.max(maxLen, dp[i]);
        return A[0].length() - maxLen;
    }
}
```

