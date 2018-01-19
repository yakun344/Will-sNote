## Add Binary
_update Jan 19, 2018  17:30_

---
[LeetCode](https://leetcode.com/problems/add-binary/description/)

Given two binary strings, return their sum (also a binary string).

**For example,**

    a = "11"
    b = "1"
    Return "100".
    
<br>

### Basic Idea:
从右往左，逐位相加，保存 carrier，结果存入 StringBuilder，返回前 reverse。关键注意这里coding的框架，算是一个类似相加题目的范例吧。
* #### Java Code:
```java
