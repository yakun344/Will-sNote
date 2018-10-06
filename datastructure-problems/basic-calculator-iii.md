# Basic Calculator III
_update 2018-10-05 22:41:18_

---
[LeetCode](https://leetcode.com/problems/basic-calculator-iii/description/)

Implement a basic calculator to evaluate a simple expression string.

The expression string may contain open ( and closing parentheses ), the plus `+` or minus sign `-`, non-negative integers and empty spaces .

The expression string contains only non-negative integers, `+, -, *, /` operators , open ( and closing parentheses ) and empty spaces . The integer division should truncate toward zero.

You may assume that the given expression is always valid. All intermediate results will be in the range of `[-2147483648, 2147483647]`.

**Some examples:**

```java
    "1 + 1" = 2
    " 6-4 / 2 " = 4
    "2*(5+5*2)/3+(6/2+8)" = 21
    "(2+6* 3+5- (3*14/7+2)*5)+3"=-12
```

**Note:** Do not use the eval built-in library function.

<br/>

### Basic Idea:
和 **Basic Calculator II** 类似，只是需要将每个括号中的内容当做一个数来处理，调用自身处理括号内容。

* #### Java Code:
  ```java
  class Solution {
      public int calculate(String s) {
          s += "+";
          Deque<Integer> stack = new ArrayDeque<>();
          char op = '+';
          int num = 0;
          for (int i = 0; i < s.length(); ++i) {
              char c = s.charAt(i);
              if (c <= '9' && c >= '0') {
                  num = num * 10 + c - '0';
              } else if (c == '(') {
                // 遇到 "(" 先处理括号中的内容
                  int right = findRight(s, i);
                  num = calculate(s.substring(i + 1, right));
                  i = right;
              } else if (c == ' ') {
                  continue;
              } else {
                // 遇到 op 的时候处理上一个 op，然后 op = c
                  if (op == '+' || op == '-') {
                      stack.offerLast(op == '+' ? num : -num);
                  } else if (op == '*') {
                      stack.offerLast(stack.pollLast() * num);
                  } else if (op == '/') {
                      stack.offerLast(stack.pollLast() / num);
                  }
                  num = 0;
                  op = c;
              }
          }
          int ret = 0;
          while (! stack.isEmpty()) ret += stack.pollLast();
          return ret;
      }

      private int findRight(String s, int left) {
          int count = 0;
          while (left != s.length() - 1) {
              char c = s.charAt(left);
              if (c == '(') count++;
              else if (c == ')') count--;
              if (count == 0) return left;
              left++;
          }
          return -1;
      }
  }
  ```
