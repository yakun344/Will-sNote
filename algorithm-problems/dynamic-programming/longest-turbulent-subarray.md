# Longest Turbulent Subarray

_update Feb 13 2019, 1:53_

[LeetCode](https://leetcode.com/problems/longest-turbulent-subarray/)

A subarray `A[i], A[i+1], ..., A[j]` of A is said to be turbulent if and only if:

For `i <= k < j, A[k] > A[k+1]` when k is odd, and `A[k] < A[k+1]` when k is even; OR, for `i <= k < j, A[k] > A[k+1]` when k is even, and `A[k] < A[k+1]` when k is odd. That is, the subarray is turbulent if the comparison sign flips between each adjacent pair of elements in the subarray.

Return the length of a maximum size turbulent subarray of A.

**Example 1:**

```text
Input: [9,4,2,10,7,8,8,1,9]
Output: 5
Explanation: (A[1] > A[2] < A[3] > A[4] < A[5])
```

**Example 2:**

```text
Input: [4,8,12,16]
Output: 2
```

**Example 3:**

```text
Input: [100]
Output: 1
```

**Note:**

1. `1 <= A.length <= 40000`
2. `0 <= A[i] <= 10^9`

## Basic Idea:

这道题就是求最长的一增一减的subarray长度。我们可以利用DP的思路，维持两个数组，每个`index i`分别表示以`A[i]` 为较大值和较小值结尾的最长符合条件subarray的长度。最后返回值就是所有长度中的最大值。

### Java Code:

```java
class Solution {
    public int maxTurbulenceSize(int[] A) {
        if (A == null || A.length == 0) return 0;
        int[] larger = new int[A.length];
        int[] smaller = new int[A.length];
        Arrays.fill(larger, 1);
        Arrays.fill(smaller, 1);
        int ret = 1;
        for (int i = 1; i < A.length; ++i) {
            if (A[i] > A[i - 1]) larger[i] += smaller[i - 1];
            else if (A[i] < A[i - 1]) smaller[i] += larger[i - 1];
            ret = Math.max(smaller[i], ret);
            ret = Math.max(larger[i], ret);
        }
        return ret;
    }
}
```

