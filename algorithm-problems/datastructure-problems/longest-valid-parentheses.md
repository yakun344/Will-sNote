# Longest Valid Parentheses

_update Nov 10,2018_

[LeetCode](https://leetcode.com/problems/longest-valid-parentheses/)

Given a string containing just the characters '\(' and '\)', find the length of the longest valid \(well-formed\) parentheses substring.

**Example 1:**

```text
Input: "(()"
Output: 2
Explanation: The longest valid parentheses substring is "()"
```

**Example 2:**

```text
Input: ")()())"
Output: 4
Explanation: The longest valid parentheses substring is "()()"
```

## Basic Idea:

因为是括号匹配问题，想到利用stack，但是因为需要找到最长的 valid substring，我们需要在stack中存入index，并且需要存入无法匹配的有括号的index来标记左边界。另外，初始的时候需要现在stack中存入index：-1，标记最初的左边界。

具体地，进行一次从左到右的扫描，如果遇到 `(`，则将其index压栈。如果遇`)`，可以的话将栈顶对应 `(` pop出来，然后计算当前长度为当前index减去当前栈顶。如果当前 `)` 是invalid的，则将当前index压栈。

### Java Code:

```java
class Solution {
    public int longestValidParentheses(String s) {
        int ret = 0;
        Deque<Integer> stack = new ArrayDeque<>();
        stack.offerLast(-1);
        for (int i = 0; i < s.length(); ++i) {
            char c = s.charAt(i);
            if (c == '(') {
                stack.offerLast(i);
            } else if (c == ')') {
                if (! stack.isEmpty()) {
                    int prev = stack.pollLast();
                    if (prev != -1 && s.charAt(prev) == '(') {
                        ret = Math.max(ret, i - stack.peekLast());
                    } else {
                        stack.offerLast(i);
                    }
                } else {
                    stack.offerLast(i);
                }
            }
        }
        return ret;
    }
}
```

