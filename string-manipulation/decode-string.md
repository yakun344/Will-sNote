## Decode String
_update Aug 10,2017  23:38_

---
[LeetCode](https://leetcode.com/problems/decode-string/description/)

Given an encoded string, return it's decoded string.

The encoding rule is: k[encoded_string], where the encoded_string inside the square brackets is being repeated exactly k times. Note that k is guaranteed to be a positive integer.

You may assume that the input string is always valid; No extra white spaces, square brackets are well-formed, etc.

Furthermore, you may assume that the original data does not contain any digits and that digits are only for those repeat numbers, k. For example, there won't be input like 3a or 2[4].

**Examples:**
    
    s = "3[a]2[bc]", return "aaabcbc".
    s = "3[a2[c]]", return "accaccacc".
    s = "2[abc]3[cd]ef", return "abcabccdcdcdef".
    
#### Basic Idea:
使用两个stack，一个存数字，一个存string。对于每个 '[' 先在stack2中插入一个空串作为这组括号最终的结果。遇到 ']' 时计算当前括号的结果。开始之前，在栈底放一个空串，作为最终结果的位置。

#### Code:
```java
    public class Solution {
        public String decodeString(String s) {
            Deque<Integer> stack1 = new LinkedList<>(); // 存字符串重复次数
            Deque<String> stack2 = new LinkedList<>(); 
            stack2.addLast(""); // 为最终结果预留位置
            for (int i = 0; i < s.length(); ++i) {
                // 如果当前字符是数字，则往后查看提取完整数字，存入stack1
                if ('0' <= s.charAt(i) && s.charAt(i) <= '9') {
                    int start = i;
                    while ('0' <= s.charAt(i) && s.charAt(i) <= '9') i++;
                    stack1.addLast(Integer.parseInt(s.substring(start, i)));
                }
                // 如果是 ‘[’，则在stack2中push一个空串“”，作为这一对括号的预留位置
                if (s.charAt(i) == '[') stack2.addLast("");
                // 表示一对括号结束，计算结果，存入stack2之前为‘]’预留的位置
                else if (s.charAt(i) == ']') {
                    int time = stack1.removeLast();
                    String p = stack2.removeLast();
                    StringBuilder sb = new StringBuilder();
                    for (int j = 0; j < time; ++j) sb.append(p);
                    stack2.addLast(stack2.removeLast() + sb.toString());
                } 
                else {
                    stack2.addLast(stack2.removeLast() + s.charAt(i));
                }
            }
            return stack2.removeLast(); 
        }
    }
```

```python
    class Solution(object):
        def decodeString(self, s):
            """
            :type s: str
            :rtype: str
            """
            stack1 = []
            stack2 = []
            stack2.append('')
            i = 0
            while i < len(s):
                num = ''
                print stack2
                if s[i].isdigit():
                    while s[i].isdigit(): 
                        num += s[i]
                        i += 1
                    stack1.append(int(num))
                if s[i] == '[':
                    stack2.append('')
                elif s[i] == ']':
                    p = stack2.pop() * stack1.pop()
                    stack2[-1] += p
                else:
                    stack2[-1] += s[i]
                i += 1
            return stack2[-1]
```