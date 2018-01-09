## Combination Sum II
_update Jul 21, 2017 18:21_

---
[LintCode](http://www.lintcode.com/en/problem/combination-sum-ii/)
[LeetCode](https://leetcode.com/problems/combination-sum-ii/description/)


Given a collection of candidate numbers (C) and a target number (T), find all unique combinations in C where the candidate numbers sums to T.

Each number in C may only be used once in the combination.

**Notice**

*  All numbers (including target) will be positive integers.
*  Elements in a combination (a1, a2, … , ak) must be in non-descending order. (ie, a1 ≤ a2 ≤ … ≤ ak).
*  The solution set must not contain duplicate combinations.

**Example**    

Given candidate set [10,1,6,7,2,1,5] and target 8,
    
    A solution set is:
    
    [
      [1,7],
      [1,2,5],
      [2,6],
      [1,1,6]
    ]
    
#### Basic Idea:
这道题目和 Combination Sum I 的区别主要是有重复元素，而且每个只能用一次。所以重点就是如何避免重复。方法是，在每次 for loop 考虑pos之后的序列的时候，只考虑第一个重复的值。

#### Java Code:
```java
    public class Solution {
        /**
         * @param num: Given the candidate numbers
         * @param target: Given the target number
         * @return: All the combinations that sum to target
         */
         
        // 这道题目有重复元素，每个元素可以使用一次，所以关键是选择元素时候避免重复
        // 方法就是在for pos往后的元素时，只选第一个相同的元素
        public List<List<Integer>> combinationSum2(int[] num, int target) {
            List<List<Integer>> res = new ArrayList<>();
            if (num.length == 0 || target <= 0) {
                return res;
            }
            Arrays.sort(num);
            helper(num, 0, new ArrayList<Integer>(), res, target);
            return res;
        }
        
        private void helper(int[] nums, 
                            int pos, 
                            List<Integer> path, 
                            List<List<Integer>> res,
                            int remainSum) {
            if (remainSum == 0) {
                res.add(new ArrayList<Integer>(path));
                return;
            }          
            for (int i = pos; i < nums.length; ++i) {
        // 结果避免重复的关键就是这里：
                if (i != pos && nums[i] == nums[i - 1]) continue;
                if (nums[i] > remainSum) break; // 因为是升序排列, 所以无需继续
                path.add(nums[i]);
                helper(nums, i + 1, path, res, remainSum - nums[i]);
                path.remove(path.size() - 1);
            }
        }
    }
```
    
