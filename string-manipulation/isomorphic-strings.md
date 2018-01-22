## Isomorphic Strings
_update Jan 22, 2018  11:18_

---
[LeetCode](https://leetcode.com/problems/isomorphic-strings/description/)

Given two strings s and t, determine if they are isomorphic.

Two strings are isomorphic if the characters in s can be replaced to get t.

All occurrences of a character must be replaced with another character while preserving the order of characters. No two characters may map to the same character but a character may map to itself.

**For example**

    Given "egg", "add", return true.
    
    Given "foo", "bar", return false.
    
    Given "paper", "title", return true.

**Note:**    
You may assume both s and t have the same length.

<br>

### Basic Idea:
基本思想就是利用两个 HashTable 分别存储对应char的相互映射关系，例如：
```python
    input:       s = "foo",       t = "bar"
    when i = 0:  {f:b}            {b:f}            , 两者相同，通过
    when i = 1:  {f:b, o:a}       {b:f, a:o}       , 通过
    when i = 2:  {f:b, o:a, o:r}  {b:f, a:o, r:o}  , return false
                  这里出错，o已经map
                  到a
```
可以用两个256长度的数组代替hashmap进行优化，时间复杂度 `O(n)`;

### Java Code:
```java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        char[] smap = new char[256];
        char[] tmap = new char[256];
        for (int i = 0; i < s.length(); ++i) {
            char sc = s.charAt(i), tc = t.charAt(i);
            if (smap[sc] != 0 && smap[sc] != tc) return false;
            if (tmap[tc] != 0 && tmap[tc] != sc) return false;
            smap[sc] = tc;
            tmap[tc] = sc;
        }
        return true;
    }
}
```