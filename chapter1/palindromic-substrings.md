## Palindromic Substrings
_update Aug 13,2017  23:40_

---
[LeetCode](https://leetcode.com/problems/palindromic-substrings/description/)

Given a string, your task is to count how many palindromic substrings in this string.

The substrings with different start indexes or end indexes are counted as different substrings even they consist of same characters.

**Example 1:**

    Input: "abc"
    Output: 3
    Explanation: Three palindromic strings: "a", "b", "c".

**Example 2:**

    Input: "aaa"
    Output: 6
    Explanation: Six palindromic strings: "a", "a", "a", "aa", "aa", "aaa".
    
**Note:**
The input string length won't exceed 1000.

#### Basic Idea:
题目要求所有的回文序列个数，最直观的方法就是以每个字符为中心，向两边扩张。扩张的同时要考虑单数和双数的情况。对于单数，则 `expand(i, i)`，对于双数，则是 `expand(i, i+1)`。

#### Python Code:
```python
    class Solution(object):
        def countSubstrings(self, s):
            """
            :type s: str
            :rtype: int
            """
            # 以left和right为初始位置，向两边扩张，每次得到新回文序列，则count++
            def expand(left, right):
                while left <= right and left >= 0 and right < len(s) and s[left] == s[right]:
                    self.count += 1
                    left -= 1
                    right += 1
    
            self.count = 0
            for i in range(len(s)):
                expand(i, i)
                expand(i, i + 1)
            return self.count
```