# Maximum Subarray

update _Jun 27, 2017_

[leetcode](https://leetcode.com/problems/maximum-subarray/#/description) Find the contiguous subarray within an array (containing at least one number) which has the largest sum.

For example, given the array \[-2,1,-3,4,-1,2,1,-5,4], the contiguous subarray \[4,-1,2,1] has the largest sum = 6.

**Follow up**: If you have figured out the O(n) solution, try coding another solution using the divide and conquer approach, which is more subtle.

**思路：**

使用dp的思路考虑。首先我们把问题转化为求包含每个元素作为最后一个元素的最大连续和 sequence。如此一来，我们就可以轻易地得出 dp 的递推关系式：`dp[n] = max{dp[n-1] + nums[n], nums[n]}`，即对每个元素，我们考虑是将其append到前一个sequence之后还是另外开始一个新的sequence，判断标准就是看哪种情况下sequence的和更大。**精髓**就是如果 `dp[n-1]<0`，我们一定会选择重新开始一个sequence。而最终我们需要的结果，就是所有 dp table中的最大值。

这其实也就是传说中的** Kadane's Algorithm **的思路。

java code：

```java
    // java
    public class Solution {
        int maxSum = 0;
        public int maxSubArray(int[] nums) {
            if (nums.length == 0) return 0;
            maxSum = nums[0];
            int curr = nums[0];
            for (int i  = 1; i < nums.length; ++i) {
                curr = curr < 0 ? nums[i] : curr + nums[i];
                maxSum = curr > maxSum ? curr : maxSum;
            }
            return maxSum;
        }
    }
```
