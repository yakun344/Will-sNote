# Maximum Product Subarray

_update Jan 17, 2018 16:37_

[LeetCode](https://leetcode.com/problems/maximum-product-subarray/description/)

Find the contiguous subarray within an array \(containing at least one number\) which has the largest product.

**For example,**

```text
given the array [2,3,-2,4],
the contiguous subarray [2,3] has the largest product = 6.
```

## Basic Idea:

  和之前的 Maximum Subarray 类似，这次要求最大连续乘积。因为 input array 中都是整数，所以可以保证其乘积的绝对值是递增的，而由于正负得负，负负得正，所以当前最大值有可能是因为当前最小值（负数）乘上一个负数得到，也有可能由当前最大值（正数）乘上一个正数得到。所以我们可以通过维持两个array，分别表示当前最大乘积和最小乘积来计算下一个位置的最大最小乘积。具体地：

  我们有如下 induction rule：

```java
    Induction Rule:
        maxProd[i] = max{ maxProd[i-1] * nums[i], minProd[i-1] * nums[i], nums[i] };
        minProd[i] = min{ maxProd[i-1] * nums[i], minProd[i-1] * nums[i], nums[i] };

    Base Case:
        maxProd[0] = nums[0];
        minProd[0] = nums[0];

    其中 maxProd, minProd 两个数组中的element i 表示包含 nums[i] 在内的最大（小）乘积；
```

  时间复杂度为 `O(n)`, 空间复杂度 `O(n)`； 空间可以被优化为 `O(1)`，因为每次都只会用到 `i-1` 位置的元素;

### Java Code:

```java
class Solution {
    public int maxProduct(int[] nums) {
        int[] maxProd = new int[nums.length];
        int[] minProd = new int[nums.length];
        maxProd[0] = nums[0];
        minProd[0] = nums[0];
        int globalMax = nums[0];
        for (int i = 1; i < nums.length; ++i) {
            int num = nums[i];
            maxProd[i] = Math.max(Math.max(maxProd[i - 1] * num, minProd[i - 1] * num), num);
            minProd[i] = Math.min(Math.min(maxProd[i - 1] * num, minProd[i - 1] * num), num);
            globalMax = Math.max(globalMax, maxProd[i]);
        }
        return globalMax;
    }
}
```

