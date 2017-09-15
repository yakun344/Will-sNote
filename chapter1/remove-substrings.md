## Remove Substrings
_update Sep 14,2017  21:40_

---
[LeetCode](http://www.lintcode.com/en/problem/remove-substrings/)

Given a string s and a set of n substrings. You are supposed to remove every instance of those n substrings from s so that s is of the minimum length and output this minimum length.

**Example**

    Given s = ccdaabcdbb, substrs = ["ab", "cd"]
    Return 2

**Explanation: **

ccdaabcdbb -> ccdacdbb -> cabb -> cb (length = 2)

#### Basic Idea:
因为移除 substring 的顺序会对结果造成影响，我们需要使用 bfs 搜索所有可能的删除顺序，输出最短的最终string长度。

1.  使用 `s.indexOf(substring, pos)`(java) or `s.index(substring, pos)`(python) 获得要删除的 substring 的index，将其删除之后加入 queue，同时加入 visited；
2.  当所有 substring 都不在结果中出现时，也就是 queue 为空时，结束；

#### Java Code:
```java
