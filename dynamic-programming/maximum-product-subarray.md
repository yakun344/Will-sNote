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

&emsp; 我们有