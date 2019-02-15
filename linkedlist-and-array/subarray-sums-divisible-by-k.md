# Subarray Sums Divisible by K
_update Feb 15 2019, 15:30_

---
[LeetCode](https://leetcode.com/problems/subarray-sums-divisible-by-k/)

Given an array A of integers, return the number of (contiguous, non-empty) subarrays that have a sum divisible by K.


**Example 1:**

    Input: A = [4,5,0,-2,-3,1], K = 5
    Output: 7
    Explanation: There are 7 subarrays with a sum divisible by K = 5:
    [4, 5, 0, -2, -3, 1], [5], [5, 0], [5, 0, -2, -3], [0], [0, -2, -3], [-2, -3]
 
**Note:**

1. `1 <= A.length <= 30000`
2. `-10000 <= A[i] <= 10000`
3. `2 <= K <= 10000`

<br/>

### Basic Idea:
既然是subarray sum的问题，仍然要从前缀和的角度考虑。题目所求的是sum为k的倍数的subarray的个数，brute force是 `O(N^2)`，所以我们的目标是 `O(N)` 时间。

我们发现对于当前preSum，如果有 `currPreSum - prevPreSum == c * K`, 则之间的部分sum就是K的倍数。进一步，如果有 `currPreSum % K == prevPreSum % K`, 这和前面的条件等价。

于是我们就有了一个解法，用一个长度为K的count数组记录之前出现的`preSum % K`的值的个数。每次计算出新的preSum之后，count数组中对应相同余数出现过的个数就是到当前num结尾的符合条件subarray的个数。然后更新count数组，加一。

#### Java Code:
注意Java中mod会产生负数，需要特别处理。
```java
class Solution {
    public int subarraysDivByK(int[] A, int K) {
        int[] count = new int[K];
        count[0] = 1;
        int preSum = 0, ret = 0;
        for (int num : A) {
            preSum += num;
            int remainder = preSum % K;
            if (remainder < 0) remainder += K;
            ret += count[remainder];
            count[remainder]++;
        }
        return ret;
    }
}
```
