#  Product of Array Except Self (Medium)
_update Feb 10,2018  19:26_

---
[LeetCode](https://leetcode.com/problems/product-of-array-except-self/description/)


Given an array of n integers where `n > 1`, nums, return an array output such that `output[i]` is equal to the product of all the elements of nums except `nums[i]`.

Solve it without division and in `O(n)`.

**For example:**

    given [1,2,3,4], return [24,12,8,6].

**Follow up:**  
Could you solve it with constant space complexity? (Note: The output array does not count as extra space for the purpose of space complexity analysis.)

<br>

### Basic Idea:
* #### 思路 1：Using division
如果可以使用除法，我们可以先求出所给定数列的连乘积，那么结果就是连乘积除以每个数字的商；

* #### 思路 2：Without division
如果不用division，就需要使用一些特殊的技巧。这里，我们注意到对于如下情况：
```python
                      0  1  2  3  4
    input:    nums = [1, 2, 3, 4, 5]

    对于 nums[2], 它对应的解为 nums[0] 到 nums[1] 的乘积乘以 nums[3] 到 nums[4] 的乘积
```
所以，事实上我们可以预处理input，生成两个数组，分别存储从左到右和从右到左的 prefix product，然后对于每个位置的解就是其左边部分乘积和右边部分乘积相乘。总的时间复杂度为 O(n);
  *  #### Java Code：
  ```java
         class Solution {
            public int[] productExceptSelf(int[] nums) {
                int[] left = new int[nums.length];
                int[] right = new int[nums.length];
                int[] ret = new int[nums.length];
                int t = 1;
                for (int i = 0; i < nums.length; ++i) {
                    left[i] = t * nums[i];
                    t = left[i];
                }
                t = 1;
                for (int i = nums.length - 1; i >= 0; --i) {
                    right[i] = t * nums[i];
                    t = right[i];
                }
                for (int i = 0; i < ret.length; ++i) {
                    int leftProduct = i == 0 ? 1 : left[i - 1];
                    int rightProduct = i == ret.length - 1 ? 1 : right[i + 1];
                    ret[i] = leftProduct * rightProduct;
                }
                return ret;
            }
        }
``` 