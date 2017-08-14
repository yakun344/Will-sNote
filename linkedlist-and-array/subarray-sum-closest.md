## Subarray Sum Closest
_update Jul, 26 2017 14:02_

---
[LintCode](http://www.lintcode.com/en/problem/subarray-sum-closest/)

Given an integer array, find a subarray with sum closest to zero. Return the indexes of the first number and last number.

**Example**

Given [-3, 1, 1, -3, 5], return [0, 2], [1, 3], [1, 1], [2, 2] or [0, 4].

#### Basic Idea:
和前面的 Subarray Sum 类似，这道题也要利用到 prefix sum array 的性质。我们知道通过前缀和数组可以**把任意一段 subarray 的和的问题转换为 sums 数组中两数之差**。

而这道题求的是**和最接近0的subarray**，我们只要找到两个最接近的前缀和即可。所以我们可以令sums为一个二维数组，在其中保存index，然后对其按 `key = sums[i][0]` 进行排序，然后逐对对比，找最接近的两前缀和，然后从 `sums[i][1]` 读出index即可。

#### Java Code：
```java
    public class Solution {
        /**
         * @param nums: A list of integers
         * @return: A list of integers includes the index of the first number 
         *          and the index of the last number
         */
        public int[] subarraySumClosest(int[] nums) {
            int[][] sums = new int[nums.length + 1][2];
            for (int i = 0; i < nums.length; ++i) {
                sums[i + 1][0] = nums[i] + sums[i][0];
                sums[i + 1][1] = i + 1;
            }
            // sort
            Arrays.sort(sums, new Comparator<int[]>() {
                public int compare(int[] a, int[] b) {
                    return a[0] - b[0];
                }  
            });
            // 逐对找最接近的
            int[] ret = new int[2];
            long minDiff = 1 + (long)Integer.MAX_VALUE;
            for (int i = 1; i < sums.length; ++i) {
                int diff = Math.abs(sums[i][0] - sums[i - 1][0]);
                if (diff < minDiff) {
                    minDiff = diff;
                    ret[0] = sums[i][1];
                    ret[1] = sums[i - 1][1];
                }
            }
            Arrays.sort(ret);
            ret[1]--;
            return ret;
        }
    }
```

#### Python Code:
```python
    class Solution:
        """
        @param nums: A list of integers
        @return: A list of integers includes the index of the first number 
                 and the index of the last number
        """
        def subarraySumClosest(self, nums):
            if len(nums) == 1:
                return [0, 0]
            # create prefix-sum array: sums
                # eg: 
                # nums: [1, 2, 3, 4]
                # sums: [1, 3, 6,10]
                #       [0, 1, 2, 3]
            sums = [[0]* 2 for i in range(len(nums))]
            sums[0][0] = nums[0]
            sums[0][1] = 0
            for i in range(1, len(sums)):
                sums[i][0] = nums[i] + sums[i - 1][0]
                sums[i][1] = i
            
            # sort according to prefix-sum
            sums.sort(key = lambda a : a[0])
            
            # 遍历，找最小差值的prefix-sum对
            ret = [0, 0]
            minDiff = float('INF')
            for i in range(1, len(sums)):
                diff = abs(sums[i][0] - sums[i - 1][0])
                if diff < minDiff:
                    minDiff = diff
                    ret = [sums[i][1], sums[i - 1][1]]
            ret.sort()
            ret[0] += 1
            return ret
```

