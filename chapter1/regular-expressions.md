## Regular Expressions
Regex 是处理string非常有力的工具。使用Java可以很方便地利用regex。这里收录一些和regex相关的题目。

---
_Jun 9, 2017 update_ 
### [459, Repeated Substring Pattern](https://leetcode.com/problems/repeated-substring-pattern/#/description)
Given a non-empty string check if it can be constructed by taking a substring of it and appending multiple copies of the substring together. You may assume the given string consists of lowercase English letters only and its length will not exceed 10000.
#### Solution:
    1 用循环brute force 硬找，利用字串长度是source 长度的约数简化运算，O(n^2);
    2 复制一遍source头尾相接，去掉首尾两个Character，看剩下的字符串中是否contians source，O(n);
    3 利用KMP的最长公共前后缀table，O(n);
    4 利用regex
    
Regex code:
```java
    // java
     public boolean repeatedSubstringPattern(String s) {
         Pattern p = Pattern.compile("(\\w+)\\1+");
         Matcher m = p.matcher(s);
         return m.matches();
     }
     // 或者直接用 return s.matches("regex"); 这样就是java one line。
``` 
