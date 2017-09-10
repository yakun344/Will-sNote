## Rotate Array
_update Sep 9,2017  23:00_

---
[LeetCode](https://leetcode.com/problems/rotate-array/description/)

Rotate an array of n elements to the right by k steps.

For example, with n = 7 and k = 3, the array [1,2,3,4,5,6,7] is rotated to [5,6,7,1,2,3,4].

**Note:**  
Try to come up as many solutions as you can, there are at least 3 different ways to solve this problem.

**Hint:**  
Could you do it in-place with O(1) extra space?

#### Basic Idea:
**方法1：**  
最简单的思路就是把nums复制一遍，然后分两段复制回去。

**方法2：**  
例如对于 [1,2,3,4,5,6,7], k=3, rotate 之后为 [5,6,7,1,2,3,4].   
仔细观察上面的两个数组，我们就会发现一个规律，将结果reverse之后是 [4,3,2,1,7,6,5]，其实就是对于长度为n的数组，先将前 n-k 个reverse，然后将后 k 个reverse，然后整体 reverse，就得到了最终结果。

Java Code:
```java
    class Solution {
        public void rotate(int[] nums, int k) {
            int n = nums.length;
            k = k % n;
            reverse(nums, 0, n - k - 1); // reverse first part
            reverse(nums, n - k, n - 1); // reverse second part
            reverse(nums, 0, n - 1);     // reverse the whole array
        }
        private void reverse(int[] nums, int i, int j) {
            if (i < 0 || i >= j) return;
            while (i < j) {
                int t = nums[i];
                nums[i] = nums[j];
                nums[j] = t;
                i++;
                j--;
            }
        }
    }
```
