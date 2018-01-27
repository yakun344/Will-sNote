# Sum of Two Integers 
_update Jan 26,2018  22:09_

---
[LeetCode](https://leetcode.com/problems/sum-of-two-integers/description/) 

Calculate the sum of two integers a and b, but you are **not** allowed to use the operator + and -.

**Example:**
    
    Given a = 1 and b = 2, return 3.
    
<br>

## Basic Idea:
每次用 `a & b << 1` 计算出进位，用 `a ^ b` 算出每一位相加后 mod 2 的值，再把这两个值相加，直到进位为 0；

* ### Java Code:
