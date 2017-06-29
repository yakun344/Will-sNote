## Longest Common Prefix
_update Jun 29, 2017, 22:42:43_

---
[leetcode](https://leetcode.com/problems/longest-common-prefix/#/solutions)
Write a function to find the longest common prefix string amongst an array of strings.

#### 思路：
首先想到的是用BFS解决 shortest path 的方法，即每次比较每个string的同一位置的character看是否相同，不同则返回当前res。但是这样还是比较慢，更好的方法是先按字典顺序排序各String,然后每次只要比较首末两个string。

##### BFS 的java code：
```java
    public class Solution {
        public String longestCommonPrefix(String[] strs) {
            if (strs.length == 0) return "";
            StringBuilder sb = new StringBuilder();
            int currLength = 0;
            char currChar = '0';
            while (true) {
                if (strs[0].length() > currLength) {
                    currChar = strs[0].charAt(currLength);
                } else return sb.toString();
                for (String s : strs) {
                    if (s.length() <= currLength) return sb.toString();
                    if (s.charAt(currLength) != currChar) return sb.toString();
                }
                currLength++;
                sb.append(currChar);
            }
        }
    }
```

##### sort 的 python code：
```python
    class Solution(object):
        def longestCommonPrefix(self, strs):
            """
            :type strs: List[str]
            :rtype: str
            """
            if len(strs) == 0: return ''
            if len(strs) == 1: return strs[0]
            strs.sort()
            ret = ''
            s1 = strs[0]
            s2 = strs[-1]
            i = 0
            while True:
                if len(s1) <= i or len(s2) <= i: return ret
                if s1[i] == s2[i]:
                    ret += s1[i]
                    i += 1
                else: return ret
```