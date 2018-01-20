## Majority Element II
_update Jan 20,2018  16:49_

---
[LeetCode](https://leetcode.com/problems/majority-element-ii/description/)

Given an integer array of size n, find all elements that appear more than `⌊ n/3 ⌋` times. The algorithm should run in linear time and in O(1) space.

<br>

### Basic Idea:
**[这里](https://www.hrwhisper.me/leetcode-169-majority-element-leetcode-229-majority-element-ii/)** 是一个关于这类问题的解释，包含了一部分对于正确的性的证明，值得一看。

这道题目要求 O(1) space 和 O(n) 时间，所以 sorting 和 hashing 都不可以考虑。只能使用摩尔投票法，因为要找的是数量大于 `1/3 * n` 的众数，可以保证解的个数不会超过 2，所以我们维持两个当前解，遍历数组一次，沿途更新两个解的值以及其对应 counter 的值。结束之后，我们还需要再scan一遍数组判断所找到的众数是否真的是解，因为解有可能不存在。









