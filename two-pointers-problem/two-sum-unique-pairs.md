## Two Sum - Unique pairs
_update Aug 19,2017  17:25_

---
[LintCode](http://www.lintcode.com/en/problem/two-sum-unique-pairs/)

Given an array of integers, find how many unique pairs in the array such that their sum is equal to a specific target number. Please return the number of pairs.

**Example**

    Given nums = [1,1,2,45,46,46], target = 47
    return 2
    
    1 + 46 = 47
    2 + 45 = 47
    
#### Basic Idea:
使用 2 pointers algorithm：

1.  先排序；
2.  left 和 right 两个指针从两端向中间逼近，沿途记录信息；
3.  注意去重的手法；

#### Java Code:
```java
    public class Solution {
        /**
         * @param nums an array of integer
         * @param target an integer
         * @return an integer
         */
        public int twoSum6(int[] nums, int target) {
            Arrays.sort(nums);
            int left = 0, right = nums.length - 1;
            int ret = 0;
            while (left < right) {
                int sum = nums[left] + nums[right];
                if (sum == target) {
                    ret++;
                    left++;
                    right--;
            // ！！！！！！！！ 去重 ！！！！！！！！
                    while (left < right && nums[left] == nums[left - 1]) left++;
                    while (left < right && nums[right] == nums[right + 1]) right--;
                }
                else if (sum < target) left++;
                else right--;
            }
            return ret;
        }
    }
```
    
    