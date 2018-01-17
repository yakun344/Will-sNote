## Maximum Product Subarray
_update Jan 17, 2018 16:37_

---
[LeetCode](https://leetcode.com/problems/maximum-product-subarray/description/)

Find the contiguous subarray within an array (containing at least one number) which has the largest product.

**For example,** 

    given the array [2,3,-2,4],
    the contiguous subarray [2,3] has the largest product = 6.


### Basic Idea:
&emsp; 和之前的 Maximum Subarray 类似，这次要求最大连续乘积。因为 input array 中都是整数，所以可以保证其乘积的绝对值是递增的，而由于正负得负，负负得正，所以当前最大值有可能是因为当前最小值（负数）乘上一个负数得到，也有可能由当前最大值（正数）乘上一个正数得到。所以我们可以通过维持两个array，分别表示当前最大乘积和最小乘积来计算下一个位置的最大最小乘积。具体地：  

&emsp; 我们有如下 induction rule：
```java
    Induction Rule:
        maxProduct[i] = max{ maxProduct[i-1] * nums[i], minProduct[i-1] * nums[i], nums[i] };
        minProduct[i] = min{ maxProduct[i-1] * nums[i], minProduct[i-1] * nums[i], nums[i] };
    
    Base Case:
        maxProduct[0] = nums[0];
        minProduct[0] = nums[0];
        
    其中 maxProduct, minProduct 两个数组中的element i 表示包含 nums[i] 在内的最大（小）乘积；
```

&emsp; 时间复杂度为 `O(n)`, 空间复杂度 `O(n)`； 空间可以被优化为 `O(1)`，因为每次都只会用到 `i-1` 位置的元素;

#### Java Code:
```java
