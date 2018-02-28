# Pow(x, n) 
_update Feb 28, 2018  16:38_

---
[LeetCode](https://leetcode.com/problems/powx-n/description/)

Implement pow(x, n).

**Example 1:**

    Input: 2.00000, 10
    Output: 1024.00000
    
**Example 2:**
    
    Input: 2.10000, 3
    Output: 9.26100
    
    
## Basic Idea:
利用 `n^2a = n^a * n^a`，可以实现 `log(n)` 时间复杂度的解法。但是要注意当 n 为负数的时候，要做底倒指反的预处理。

* ### Recursive Solution (Java):
```java


* ### Iterative Solution (Java):
```java
