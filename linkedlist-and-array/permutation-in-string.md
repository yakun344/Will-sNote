## Permutation in String
_update Jan 10, 2018 15:54_

---
[LeetCode](https://leetcode.com/problems/permutation-in-string/description/)

Given two strings s1 and s2, write a function to return true if s2 contains the permutation of s1. In other words, one of the first string's permutations is the substring of the second string.

**Example 1:**
    
    Input:s1 = "ab" s2 = "eidbaooo"
    Output:True
    Explanation: s2 contains one permutation of s1 ("ba").

**Example 2:**

    Input:s1= "ab" s2 = "eidboaoo"
    Output: False

**Note:**
  
  1. The input strings only contain lower case letters.
  2. The length of both given strings is in range [1, 10,000].
  
<br>

### Basic Idea
**Sliding window**， window size 为 `len(s1)`，每次移动 window 后检查当前 window 内的字符串是否和 s1 有着完全相同的字母组成，是的话则返回 true，例如：
``` 
    s1:  abdc
    s2:  aksdjl | bdac | kljdl
          ->    l      r    ->
    这种情况的时候，window中的 bdac 有着和 s1 相同的字母组成，返回 true；
```
具体地，判断窗口内字母和 s1 是否组成相同的话，可以先用一个 `int[26] map` 存储 s1 的每个字母的数量，并且维持一个 count 初始化为 `len(s1)`。随着窗口的移动，对 map 数组和 count 进行更新，直到 count==0 时，返回 true.

#### Java Implementation
```java
