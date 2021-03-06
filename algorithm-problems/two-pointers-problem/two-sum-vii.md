---
description: 'update Mar 5, 2021'
---

# Two Sum VII



Given an array of integers that is already sorted in ascending absolute order, find two numbers so that the sum of them equals a specific number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Note: the subscript of the array starts with 0

You are not allowed to sort this array.

#### Example

```text
Input: 
[0,-1,2,-3,4]
1
Output: [[1,2],[3,4]]
Explanation: nums[1] + nums[2] = -1 + 2 = 1, nums[3] + nums[4] = -3 + 4 = 1
You can return [[3,4],[1,2]], the system will automatically help you sort it to [[1,2],[3,4]]. But [[2,1],[3,4]] is invaild.
```

#### Challenge

O\(n\) time complexity and O\(1\) extra space

#### Notice

* It is guaranteed that all numbers in thenumsnumsnums is distinct.
* The length ofnumsnumsnums is ≤100000.
* The number in nums is ≤10e9.

### Basic Idea

这道题目比较特殊的地方在于数组是按照绝对值排序，也就是说可能会出现正负交错的情况。如果是升序排列，我们可以使用双指针从最小值与最大值出发，O\(N\) 时间即可找到所有满足条件的pair。而对于这里的情况，由于不是简单的排序，我们没办法直接使用双指针，但是我们发现可以按照两正数，两负数，一正一负分组处理，对于每一组，我们可以做到使用双指针O\(N\)时间找到所有满足条件的pair。

### Java Code

```java
public class Solution {

    public List<List<Integer>> twoSumVII(int[] nums, int target) {
        List<List<Integer>> res = new ArrayList<>();
        int left = 0, right = nums.length - 1;
        // ab 均大于等于0
        while (left < right) {
            while (left < nums.length && nums[left] < 0) left++;
            while (right >= 0 && nums[right] < 0) right--;
            if (left >= right) break;
            if (nums[left] + nums[right] == target) {
                res.add(Arrays.asList(left, right));
                left++;
                right--;
            } else if (nums[left] + nums[right] < target) {
                left++;
            } else {
                right--;
            }
        }
        // ab 均小于0
        left = 0; right = nums.length - 1;
        while (left < right) {
            while (left < nums.length && nums[left] >= 0) left++;
            while (right >= 0 && nums[right] >= 0) right--;
            if (left >= right) break;
            if (nums[left] + nums[right] == target) {
                res.add(Arrays.asList(left, right));
                left++;
                right--;
            } else if (nums[left] + nums[right] < target) {
                right--;
            } else {
                left++;
            }
        }
        // a < 0, b > 0, 同时从右边开始，保证从最小负数以及最大正数开始
        left = nums.length - 1; right = nums.length - 1;
        while (left >= 0 && right >= 0) {
            while (left >= 0 && nums[left] >= 0) left--;
            while (right >= 0 && nums[right] <= 0) right--;
            if (left < 0 || right < 0) break;
            if (nums[left] + nums[right] == target) {
                int[] arr = new int[]{left, right};
                Arrays.sort(arr);
                List<Integer> list = new ArrayList<>();
                list.add(arr[0]);
                list.add(arr[1]);
                res.add(list);
                left--;
                right--;
            } else if (nums[left] + nums[right] < target) {
                left--;
            } else {
                right--;
            }
        }
        return res;
    }
}
```

