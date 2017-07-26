## Subarray Sum
_update Jul,26 2017 10:07_

---
[LintCode](http://www.lintcode.com/en/problem/subarray-sum/)

Given an integer array, find a subarray where the sum of numbers is zero. Your code should return the index of the first number and the index of the last number.

**Notice**

There is at least one subarray that it's sum equals to zero.

**Example**

    Given [-3, 1, 2, -3, 4], return [0, 2] or [1, 3].
    
#### Basic Idea:
利用前缀和的性质，sums[i]存放了前i个数的和。则第 `a ~ b` 个数字的和就是 `sums[b] - sums[a - 1]`。所以要找连续和为0的段，我们只要找每个数字之前有无 sums[i] == sums[this index] 即可。

#### Java Code:
```java
    public class Solution {
        /**
         * @param nums: A list of integers
         * @return: A list of integers includes the index of the first number 
         *          and the index of the last number
         */
        public ArrayList<Integer> subarraySum(int[] nums) {
            Map<Integer, Integer> sums = new HashMap<>();
            ArrayList<Integer> ret = new ArrayList<>();
            sums.put(0, 0);
            int sum = 0;
            for (int i = 0; i < nums.length; ++i) {
                sum += nums[i];
                if (sums.containsKey(sum)) {
                    ret.add(sums.get(sum));
                    ret.add(i);
                    return ret;
                }
                sums.put(sum, i + 1);
            }
            return new ArrayList<Integer>();
        }
    }
```