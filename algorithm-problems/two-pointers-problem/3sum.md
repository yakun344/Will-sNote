# 3Sum

_update Aug 19, 2017 14:12_

[LeetCode](https://leetcode.com/problems/3sum/description/)

Given an array S of n integers, are there elements `a, b, c` in S such that `a + b + c = 0`? Find all unique triplets in the array which gives the sum of zero.

Note: The solution set must not contain duplicate triplets.

**For example,** 

```text
given array S = [-1, 0, 1, 2, -1, -4],

A solution set is:
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

## Basic Idea:

从左到右，选择每个数字作为第一个数，然后针对每种情况，执行一次 2 pointers 求 2 sum 的操作。

但是要注意去重。去重的时候注意三点：

1. 首先对nums进行排序，从小到大；
2. 每次求 two sum 的起始位置（left的位置）应该是 i+1，即当前第一个数之后的那个数；
3. 在每次出现可行解之后，要用while循环让left和right向中间同时移动，跳过所有重复组合；

## Java Code:

```java
    // 从左到右，以每个数为第一个数，用双指针确定另外两数
    // 为了避免重复，相同的元素只选第一个做第一个数，如果有重复，需要跳过
    // 时间复杂度为 O(n^2)
    class Solution {
        public List<List<Integer>> threeSum(int[] nums) {
            List<List<Integer>> res = new ArrayList<>();
            if (nums == null || nums.length < 3) return res;
            Arrays.sort(nums);

            for (int i = 0; i < nums.length; ++i) {
                if (i > 0 && nums[i] == nums[i - 1]) continue;
                int left = i + 1, right = nums.length - 1;
                while (left < right) {
                    int sum = nums[i] + nums[left] + nums[right];
                    if (sum == 0) {
                        List<Integer> temp = new ArrayList<>();
                        temp.add(nums[i]);
                        temp.add(nums[left++]);
                        temp.add(nums[right--]);
                        res.add(temp);
                    // 关键两行：
                        while (left > 0 && left < right && nums[left] == nums[left - 1]) left++;
                        while (right < nums.length - 1 && left < right && nums[right] == nums[right + 1]) right--;
                    }
                    else if (sum > 0) right--;
                    else left++;
                }
            }
            return res;
        }
    }
```

