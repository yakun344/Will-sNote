## 4Sum
_update Aug 19,2017  15:26_

---
[LeetCode](https://leetcode.com/problems/4sum/description/)

Given an array S of n integers, are there elements a, b, c, and d in S such that a + b + c + d = target? Find all unique quadruplets in the array which gives the sum of target.

**Note:** The solution set must not contain duplicate quadruplets.

**For example, **

    given array S = [1, 0, -1, 0, -2, 2], and target = 0.

    A solution set is:
    [
      [-1,  0, 0, 1],
      [-2, -1, 1, 2],
      [-2,  0, 0, 2]
    ]
    
#### Basic Idea:
因为要列举所有可行的解，所以时间复杂度最优即为O(n^3)。基本思路就是先排序，然后对于每一对 first，second，用 2 pointers 找后两个数的组合。过程中要特别注意去重。

**java code:**
```java
    // 从左到右以每个数为第一个数
    // 对于每个第一个数的情况，从它右边相邻开始，从左到右选择每个数为第二个数
    // 对于每种前两个数的情况，从其右边相邻开始，执行2 pointers 的 2 sum 算法
    class Solution {
        public List<List<Integer>> fourSum(int[] nums, int target) {
            List<List<Integer>> res = new ArrayList<>();
            if (nums == null || nums.length < 4) return res;
            // 排序
            Arrays.sort(nums);
            
            for (int first = 0; first < nums.length; ++first) {
                // 对first去重
                if (first > 0 && nums[first] == nums[first - 1]) continue;
                
                for (int second = first + 1; second < nums.length; ++second) {
                    // 对second去重
                    if (second > first + 1 && nums[second] == nums[second - 1]) continue;
                    
                    int left = second + 1, right = nums.length - 1;
                    while (left < right) {
                        int sum = nums[first] + nums[second] + nums[left] + nums[right];
                        if (sum == target) {
                            List<Integer> temp = new ArrayList<>();
                            temp.add(nums[first]);
                            temp.add(nums[second]);
                            temp.add(nums[left++]);
                            temp.add(nums[right--]);
                            res.add(temp);
                            // 对left和right去重
                            while (left < right && nums[left] == nums[left - 1]) left++;
                            while (left < right && nums[right] == nums[right + 1]) right--;
                        } else if (sum > target) {
                            right--;
                        } else {
                            left++;
                        }
                    }
                }
            }
            return res;
        }
    }
```
**优化：**
之后，我们注意到上面的思路是可以优化的，因为我们已经排序，可以利用这个性质来剪枝（pruning）；
```java
    // 优化，由于数组是排序的，一定会有 nums[first] <= nums[second] <= nums[left] <= nums[right]
    // 那么，如果出现当 nums[first] * 4 > target 或者 nums[first] + nums[second] * 3 > target 之类的情况
    // 我们就可以终止搜索，因为 4 * nums[first] 一定不会大于四个数的和
    // 同样的，如果出现 nums[first] + sum(nums[倒数三个]) < target, 说明当前first太小了，可以停止搜索
    class Solution {
        public List<List<Integer>> fourSum(int[] nums, int target) {
            List<List<Integer>> res = new ArrayList<>();
            if (nums == null || nums.length < 4) return res;
            // 排序
            Arrays.sort(nums);
            
            for (int first = 0; first < nums.length - 3; ++first) {
                // 对first去重
                if (first > 0 && nums[first] == nums[first - 1]) continue;
                // 在first层优化
                if (nums[first] * 4 > target) return res;
                if (nums[first] + nums[nums.length - 1] + nums[nums.length - 2] + nums[nums.length - 3] < target) continue;
                
                for (int second = first + 1; second < nums.length - 2; ++second) {
                    // 对second去重
                    if (second > first + 1 && nums[second] == nums[second - 1]) continue;
                    // 在second 层优化
                    if (nums[first] + nums[second] * 3 > target) break;
                    if (nums[first] + nums[second] + nums[nums.length - 1] + nums[nums.length - 2] < target) continue;
                    
                    int left = second + 1, right = nums.length - 1;
                    while (left < right) {
                        // 在 3rd 层优化
                        if (nums[first] + nums[second] + nums[left] * 2 > target) break;
                        
                        int sum = nums[first] + nums[second] + nums[left] + nums[right];
                        if (sum == target) {
                            List<Integer> temp = new ArrayList<>();
                            temp.add(nums[first]);
                            temp.add(nums[second]);
                            temp.add(nums[left++]);
                            temp.add(nums[right--]);
                            res.add(temp);
                            // 对left和right去重
                            while (left < right && nums[left] == nums[left - 1]) left++;
                            while (left < right && nums[right] == nums[right + 1]) right--;
                        } else if (sum > target) {
                            right--;
                        } else {
                            left++;
                        }
                    }
                }
            }
            return res;
        }
    }
```
    
    
    
    