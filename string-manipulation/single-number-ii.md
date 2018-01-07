## Single Number II
_update Aug 9, 2017  0:13_

---
[LeetCode](https://leetcode.com/problems/single-number-ii/description/)

Given an array of integers, every element appears three times except for one, which appears exactly once. Find that single one.

**Note:**
Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

#### Basic Idea:
当然可以统计每个数字出现的次数然后返回只出现一次的数，但是这样显然太 low 了，考虑到 follow up 要求不使用 extra memory，我们寻求位运算的解法。

如下图手写笔记所示：
![](/assets/WechatIMG14.jpg)

精髓就是，我们要做到让出现了三的倍数次的位变为0，而最终留下出现了1次的数字的每一位。那么我们就通过这个三进制counter，对每个输入的数进行处理。因为其他数字都出现了恰好三次，所以他们对a，b的影响最终会抵消为0，剩下的就是另外那个数的影响，我们只要求 a|b 就可以得到结果。

#### Java Code:
```java
    public class Solution {
        public int singleNumber(int[] nums) {
            int a = 0, b = 0;
            for (int c : nums) {
                int new_a = (~a & b & c) | (a & ~b & ~c);
                int new_b = (b & ~a & ~c) | (~a & ~b & c);
                a = new_a;
                b = new_b;
            }
            return a | b;
        }
    }
```
