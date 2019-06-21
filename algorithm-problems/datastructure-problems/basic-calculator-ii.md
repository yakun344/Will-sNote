# Basic Calculator II

_update Sep 16 2018, 19:36_

[LeetCode](https://leetcode.com/problems/basic-calculator-ii/description/)

Implement a basic calculator to evaluate a simple expression string.

The expression string contains only non-negative integers, +, -, \*, / operators and empty spaces . The integer division should truncate toward zero.

**Example 1:**

```text
Input: "3+2*2"
Output: 7
```

**Example 2:**

```text
Input: " 3/2 "
Output: 1
```

**Example 3:**

```text
Input: " 3+5 / 2 "
Output: 5
```

**Note:**

1. You may assume that the given expression is always valid.
2. Do not use the eval built-in library function.

## Basic Idea:

利用stack，从左向右扫描。遇到空格就跳过，遇到数字就和之前的数字更新当前数字，遇到符号的话，加减号直接处理后加入stack，乘除号则需要直接计算好当前栈顶数字和当前数字的乘积或商再push入栈。

### Java Code：

* **使用regex预处理**

  ```java
  class Solution {
    public int calculate(String s) {
        int[] numbers = Arrays.stream(s.split("[+\\-*/]")).mapToInt((a)->{return Integer.parseInt(a.trim());}).toArray();
        String[] signs = Arrays.stream(s.split("[\\d]"))
                                .filter((a)->{return ! a.matches("\\s*");})
                                .map(a->a.trim())
                                .toArray(String[]::new);
        
        Deque<Integer> deque = new ArrayDeque<>();
        int numIndex = 0, signIndex = 0;
        deque.offerLast(numbers[numIndex++]);
        while (true) {
            if (numIndex == numbers.length) break;
            if (signs[signIndex].equals("+")) {
                deque.offerLast(numbers[numIndex]);
            } else if (signs[signIndex].equals("-")) {
                deque.offerLast(-numbers[numIndex]);
            } else if (signs[signIndex].equals("*")) {
                deque.offerLast(deque.pollLast() * numbers[numIndex]);
            } else {
                deque.offerLast(deque.pollLast() / numbers[numIndex]);
            }
            numIndex++;
            signIndex++;
        }

        int ret = 0;
        while (! deque.isEmpty()) {
            ret += deque.pollFirst();
        }
        return ret;
    }
  }
  ```

* **逐个字符扫描**

  这个写法的难点是要在遇到符号的时候处理上一个符号的运算。

  ```java
  class Solution {
    public int calculate(String s) {
        int num = 0;
        char sign = '+';
        Deque<Integer> stack = new ArrayDeque<>();

        for (int i = 0; i < s.length() + 1; ++i) {
            char c = i == s.length() ? '+' : s.charAt(i);
            if (c >= '0' && c <= '9') {
                num = num * 10 + c - '0';
                continue;
            } else if (c == ' ') {
                continue;
            } else {
                if (sign == '+') {
                    stack.offerLast(num);
                } else if (sign == '-') {
                    stack.offerLast(-num);
                } else if (sign == '*') {
                    stack.offerLast(stack.pollLast() * num);
                } else {
                    stack.offerLast(stack.pollLast() / num);
                }
                sign = c;
                num = 0;
            }
        }
        int ret = 0;
        while (! stack.isEmpty()) ret += stack.pollLast();
        return ret;
    }
  }
  ```

