# Word Pattern
_update Jan 25,2018  17:13_

---
[LeetCode](https://leetcode.com/problems/word-pattern/description/)

Given a pattern and a string str, find if str follows the same pattern.

Here follow means a full match, such that there is a bijection between a letter in pattern and a non-empty word in str.

**Examples:**

    pattern = "abba", str = "dog cat cat dog" should return true.
    pattern = "abba", str = "dog cat cat fish" should return false.
    pattern = "aaaa", str = "dog cat cat dog" should return false.
    pattern = "abba", str = "dog dog dog dog" should return false.
    
**Notes:**

You may assume pattern contains only lowercase letters, and str contains lowercase letters separated by a single space.

## Basic Idea:
使用 HashMap，但是注意要对 `<pattern, str>, <str, pattern>` 进行双向map，因为有可能出现两个 pattern 对应着同样一个string的情况，要注意。
  * ### Java Code：
  ```java
