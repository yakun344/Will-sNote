# Maximum Product of Three Numbers

_update Sep 3,2018 14:32_

---
[LeetCode](https://leetcode.com/problems/maximum-product-of-three-numbers/description/)

Given an integer array, find three numbers whose product is maximum and output the maximum product.

**Example 1:**

    Input: [1,2,3]
    Output: 6

**Example 2:**

    Input: [1,2,3,4]
      Output: 24

**Note:**

The length of the given array will be in range [3,104] and all elements are in the range [-1000, 1000].

Multiplication of any three numbers in the input won't exceed the range of 32-bit signed integer.

### Basic Idea:
求给定数组中乘积最大的三个数，考虑负数的情况，就只有两种可能：两个最小负数乘以最大的正数，或者三个最大的正数相乘。于是我们可以先求得整个数组中最小的两个数字和最大的三个数字，然后比较两种情况的乘积，取最大的。

具体的，我们可以用两个 PriorityQueue 分别求两个最小值和三个最大值，总时间复杂度为 `O(n * (log(2) + log(3))) = O(n)`.

#### Java Code:
```java
class Solution {
    public int maximumProduct(int[] nums) {
        PriorityQueue<Integer> minQ = new PriorityQueue<>();
        PriorityQueue<Integer> maxQ = new PriorityQueue<>((a, b)->{ return Integer.compare(b, a); });
        for (int num : nums) {
            minQ.offer(num);
            if (minQ.size() > 3) minQ.poll();
            maxQ.offer(num);
            if (maxQ.size() > 2) maxQ.poll();
        }
        int max3 = minQ.poll();
        int max2 = minQ.poll();
        int max1 = minQ.poll();
        int min2 = maxQ.poll();
        int min1 = maxQ.poll();
        return Math.max(min1 * min2 * max1, max1 * max2 * max3);
    }
}
```
