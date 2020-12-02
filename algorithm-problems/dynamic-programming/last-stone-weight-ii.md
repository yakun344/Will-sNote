# 1049. Last Stone Weight II

_update 2020 Dec 1_

---
[Leetcode](https://leetcode.com/problems/last-stone-weight-ii/)

```
We have a collection of rocks, each rock has a positive integer weight.

Each turn, we choose any two rocks and smash them together.  Suppose the stones have weights x and y with x <= y.  The result of this smash is:

If x == y, both stones are totally destroyed;
If x != y, the stone of weight x is totally destroyed, and the stone of weight y has new weight y-x.
At the end, there is at most 1 stone left.  Return the smallest possible weight of this stone (the weight is 0 if there are no stones left.)
```

**Example 1:**
```
Input: [2,7,4,1,8,1]
Output: 1
Explanation: 
We can combine 2 and 4 to get 2 so the array converts to [2,7,1,8,1] then,
we can combine 7 and 8 to get 1 so the array converts to [2,1,1,1] then,
we can combine 2 and 1 to get 1 so the array converts to [1,1,1] then,
we can combine 1 and 1 to get 0 so the array converts to [1] then that's the optimal value.
```

**Note:**
```
1. 1 <= stones.length <= 30
2. 1 <= stones[i] <= 100
```

## Basic Idea:
当两个石头相遇，如果重量相同，则抵消，否则生成一个两者重量相减的石头，求最后剩下一个石头的最小重量。

例如例子中的`[2,7,4,1,8,1]`，我们经过分步相消，实际上过程是：`(4-2)-(8-7)-1+1 = 4-2-8+7-1+1 = 1`。我们注意到实际上是把每个数字前面添上正号或者负号，求和使之和最小。这就将这道题转换为了另一道题 [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/description/)。我们只要将输入数组分成两个subset，一个附带正号，一个附带负号，分别求和并使两者和的差值最小即可。

仍然可以使用和那道题类似的DP思路，使用一个长度为 `sum(nums) / 2 + 1` 的 boolean dp 数组，其中 `dp[i]` 表示能否找到一个subset使得其sum等于i。最终需要返回两个subset和的差值的最小值，所以我们取dp数组中最大为true的i作为其中一个sum，另一个sum就是总和减去它，返回两个sum的差的绝对值即可。

## Java Code
```java
class Solution {
    public int lastStoneWeightII(int[] stones) {
        int sum = 0;
        for (int num : stones) {
            sum += num;
        }
        int target = sum / 2;
        boolean dp[] = new boolean[target + 1];
        dp[0] = true;
        int maxPossibleSum = 0;
        for (int num : stones) {
            // 从大到小，避免前面的计算影响后面的结果
            for (int i = target - num; i >= 0; --i) {
                if (dp[i]) {
                    dp[i + num] = true;
                    maxPossibleSum = Math.max(maxPossibleSum, i + num);
                }
            }
        }
        return sum - maxPossibleSum - maxPossibleSum;
    }
}
```
