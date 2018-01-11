## Remove Duplicate Letters （hard）
_update Jan 11, 2018  14:12_

---
[LeetCode](https://leetcode.com/problems/remove-duplicate-letters/description/)

Given a string which contains only lowercase letters, remove duplicate letters so that every letter appear once and only once. You must make sure your result is the smallest in lexicographical order among all possible results.

**Example:**

    Given "bcabc"
    Return "abc"

    Given "cbacdcbc"
    Return "acdb"
    
<br>

### Basic Idea
&emsp; 这道题目首先要求去掉所有重复字母，重复部分只保留一份copy，而且要求输出结果要保证最小的字典顺序。所以想到既需要记录每个字母出现的次数，又要记录每个字母出现的位置，或者相对顺序。  

&emsp; 使用 stack + HashMap 的思路。首先遍历一遍 input string，在 map 中记录其每个字母的个数。第二次遍历的时候，保证最终在stack中留下的顺序是最终输出时的顺序。具体地，每次先看当前 char c 是否在stack中，如果在则跳过，否则检查 c 和 peek 的大小，如果 peek 大于等于 c，而且 peek 的 count 仍然大于 0（说明后面还会出现），则 pop 掉 peek，继续检查，直到peek 小于 c，将 c push。每循环过一个 c，就将 map 中对应 c 的 count -1。
<br>
### python Code:
```python
class Solution:
    def removeDuplicateLetters(self, s):
        """
        :type s: str
        :rtype: str
        """
        stack = []
        instack = [False] * 255
        count = [0] * 255
        
        for c in s: count[ord(c)] += 1  # 统计每个字母个数
        
        # 操作stack
        for c in s:
            while stack and not instack[ord(c)] and count[ord(stack[-1])] > 0 and ord(stack[-1]) > ord(c):
                instack[ord(stack[-1])] -= 1
                stack.pop()
            if not instack[ord(c)]:
                instack[ord(c)] = True
                stack.append(c)
            count[ord(c)] -= 1
            
        return ''.join(stack)
```
