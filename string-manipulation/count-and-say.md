## Count and Say
_update Jan 19, 2018  16:46_

---
[LeetCode](https://leetcode.com/problems/count-and-say/description/)

The count-and-say sequence is the sequence of integers with the first five terms as following:
```
        1.     1
        2.     11
        3.     21
        4.     1211
        5.     111221
        1 is read off as "one 1" or 11.
        11 is read off as "two 1s" or 21.
        21 is read off as "one 2, then one 1" or 1211.
```
Given an integer n, generate the nth term of the count-and-say sequence.

**Note:**  
Each term of the sequence of integers will be represented as a string.

**Example 1:**

        Input: 1
        Output: "1"

**Example 2:**

        Input: 4
        Output: "1211"
        
<br>

### Basic Idea:
解法就是按照之前一次的结果，从左到右数相同元素的个数，生成新的结果，再往复继续。