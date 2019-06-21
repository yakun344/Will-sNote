# Partition to K Equal Sum Subsets

_update 2018-10-10 00:53:15_

[LeetCode](https://leetcode.com/problems/partition-to-k-equal-sum-subsets/description/)

Given an array of integers nums and a positive integer k, find whether it's possible to divide this array into k non-empty subsets whose sums are all equal.

**Example 1:**

```text
Input: nums = [4, 3, 2, 3, 5, 2, 1], k = 4
Output: True
Explanation: It's possible to divide it into 4 subsets (5), (1, 4), (2,3), (2,3) with equal sums.
```

**Note:**

1. 1 &lt;= k &lt;= len\(nums\) &lt;= 16.
2. 0 &lt; nums\[i\] &lt; 10000.

## Basic Idea:

这是 _416. Partition Equal Subset Sum_ 的follow up，方法也和之前的题目类似。

初拿到题目的时候感觉很蒙逼，但是仔细想了一下，发现有如下隐含信息：

1. 要把给定数组分成k个sum相等的subset，可以推出targetSum一定为整数，并且等于 `sum/k`；
2. 如果发现`sum/k`的值不是整数，可以直接返回false;

这样问题就被转换成了：求给定数组能否被分成k个subset并且他们的sum都为targetSum。那么如何求解呢？想到可以使用dfs。这里因为subset的数量已经确定了，我们可以先虚拟创建出所有的subset，然后考虑每个数字被放入不同subset的情况。这种DFS的思路和之前的题目都是不同的，需要格外注意。

起初我的想法是先创建第一个subset，然后第二个。。以此类推，但是这样不容易实现，逻辑太复杂。最后的做法是创建一个长度为 k 的数组表示每个subset的sum值，然后进行dfs，每层考虑一个index的数字会被放入哪个subset，共有`len(nums)`层。时间复杂度为`O(k^len(nums))`。

**优化思路主要有三点：**

* 如果targetSum不为整数直接返回false；
* 如果存在 `num>targetSum` 直接返回 false；
* 如果在某一层有几个subset的当前sum相等，则只需要考虑其中一个；
* 可以对nums排序，然后从大到小考虑每个数字，这样可以提前发现行不通的路；

### Java Code

```java
class Solution {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        int sum = 0;
        for (int num : nums) sum += num;
        if (sum % k != 0) return false; // 如果sum/k除不尽，说明不存在这样的partition
        sum /= k;
        for (int num : nums) if (num > sum) return false;
        int[] setList = new int[k];
        return helper(nums, setList, 0, sum);
    }

    private boolean helper(int[] nums, int[] setList, int pos, int targetSum) {
        if (pos == nums.length) return true;
        Set<Integer> currSumSet = new HashSet<>();
        for (int i = 0; i < setList.length; ++i) {
            int currSum = setList[i];
            if (! currSumSet.add(currSum)) continue;
            if (nums[pos] + currSum <= targetSum) {
                setList[i] += nums[pos];
                if (helper(nums, setList, pos + 1, targetSum)) return true;
                setList[i] -= nums[pos];
            }
        }
        return false;
    }
}
```

