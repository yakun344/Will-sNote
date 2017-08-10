## Single Number III
_update Aug 9,2017 23:08_

---
[LeetCode](https://leetcode.com/problems/single-number-iii/description/)

Given an array of numbers nums, in which exactly two elements appear only once and all the other elements appear exactly twice. Find the two elements that appear only once.

**For example:**

    Given nums = [1, 2, 1, 3, 2, 5], return [3, 5].

**Note:**
The order of the result is not important. So in the above example, [5, 3] is also correct.

Your algorithm should run in linear runtime complexity. Could you implement it using only constant space complexity?

#### Basic Idea:
先求所有数的XOR，就可以得到两个单独数的XOR。这个XOR的每一位 1 都说明 a，b 在这一位不同。我们只要找到第一位，或者最高位，然后把 nums 中所有这一位为 1 的数拉出来求出xor，就得到了 a，b 中的一个，然后另一个就是 XOR ^ 第一个。

#### Java Code:
    ```java
    class Solution(object):
        def singleNumber(self, nums):
            """
            :type nums: List[int]
            :rtype: List[int]
            """
            XOR = reduce(lambda x, y : x ^ y, nums)
            mask = 1 << 31
            while (mask & XOR == 0): mask >>= 1
            a = 0
            for n in nums:
                a = a ^ n if n & mask else a
            b = XOR ^ a
            return [a, b]
```