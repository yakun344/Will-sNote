# 1498. Number of Subsequences That Satisfy the Given Sum Condition

_update Jun 28, 2020_

[Leetcode](https://github.com/Will-GXZ/Xiaozheng_Algo/tree/4db7f500414fdb90458fcabc4feae50ac4865ba3/algorithm-problems/two-pointers-problem/!https:/leetcode.com/problems/number-of-subsequences-that-satisfy-the-given-sum-condition/README.md)

Given an array of integers nums and an integer target.

Return the number of non-empty subsequences of nums such that the sum of the minimum and maximum element on it is less or equal than target.

Since the answer may be too large, return it modulo `10^9 + 7`.

**Example 1:**

```text
Input: nums = [3,5,6,7], target = 9
Output: 4
Explanation: There are 4 subsequences that satisfy the condition.
[3] -> Min value + max value <= target (3 + 3 <= 9)
[3,5] -> (3 + 5 <= 9)
[3,5,6] -> (3 + 6 <= 9)
[3,6] -> (3 + 6 <= 9)
```

**Example 2:**

```text
Input: nums = [3,3,6,8], target = 10
Output: 6
Explanation: There are 6 subsequences that satisfy the condition. (nums can have repeated numbers).
[3] , [3] , [3,3], [3,6] , [3,6] , [3,3,6]
```

**Example 3:**

```text
Input: nums = [2,3,3,4,6,7], target = 12
Output: 61
Explanation: There are 63 non-empty subsequences, two of them don't satisfy the condition ([6,7], [7]).
Number of valid subsequences (63 - 2 = 61).
```

**Example 4:**

```text
Input: nums = [5,2,4,1,7,6,8], target = 16
Output: 127
Explanation: All non-empty subset satisfy the condition (2^7 - 1) = 127
```

**Constraints:**

```text
1 <= nums.length <= 10^5
1 <= nums[i] <= 10^6
1 <= target <= 10^6
```

## Basic Idea

这道题其实是要求所有满足条件的subset的个数，而数据规模比较大，必须用低于 `O(N^2)` 的时间复杂度来做。

首先，因为题目限定的是subset中最大和最小数字的和，让我们可以想到先对输入数组排序，然后用双指针从左右两端向中间扫描，如果两者之和比较小，则右移left，反之则左移right，这样可以把最外层的时间复杂度做到 `O(N)`。

对于每一对符合条件的左右边界，其中一定包含左端点的所有subset都符合要求，而这些subset的个数为 `2^(right-left)`。但是对于如何计算这个2的乘方需要有所处理，因为需要 mod `1e10+7`。可以利用 `a^(m+n)=a^m * a^n` 这条性质来将时间复杂度变为 `O(log(right-left))`。

总时间复杂度 `O(NlogN)`。

## Java Code:

```java
class Solution {
    public int numSubseq(int[] nums, int target) {
        Arrays.sort(nums);
        int left = 0, right = nums.length - 1;
        long ret = 0;
        while (left <= right) {
            if (nums[left] + nums[right] <= target) {
                ret = wrapper(ret + getPo2(right - left));
                left++;
            } else {
                right--;
            }
        }
        return (int) ret;
    }

    private long getPo2(long k) {
        if (k == 0) return 1;
        if (k == 1) return 2;
        long ret = getPo2(k / 2);
        if (k % 2 == 0) {
            return wrapper(ret * ret);
        } else {
            return wrapper(ret * ret * 2);
        }
    }

    private long wrapper(long k) {
        return k % (long) (1e9+7);
    }
}
```

