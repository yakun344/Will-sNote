# String to Integer \(atoi\)

_update Sep 16 2018, 16:38_

[LeetCode](https://leetcode.com/problems/string-to-integer-atoi/description/)

Implement atoi which converts a string to an integer.

The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.

The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.

If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.

If no valid conversion could be performed, a zero value is returned.

**Note:**

Only the space character ' ' is considered as whitespace character. Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: \[−231, 231 − 1\]. If the numerical value is out of the range of representable values, INT\_MAX \(231 − 1\) or INT\_MIN \(−231\) is returned.

**Example 1:**

Input: "42" Output: 42

**Example 2:**

Input: " -42" Output: -42 Explanation: The first non-whitespace character is '-', which is the minus sign. Then take as many numerical digits as possible, which gets 42.

**Example 3:**

Input: "4193 with words" Output: 4193 Explanation: Conversion stops at digit '3' as the next character is not a numerical digit.

**Example 4:**

Input: "words and 987" Output: 0 Explanation: The first non-whitespace character is 'w', which is not a numerical digit or a +/- sign. Therefore no valid conversion could be performed.

**Example 5:**

Input: "-91283472332" Output: -2147483648 Explanation: The number "-91283472332" is out of the range of a 32-bit signed integer. Thefore INT\_MIN \(−231\) is returned.

## Basic Idea:

将字符串数字转为int，按顺序分别判断如果当前字符是符号，数字或者是其他字符该如何处理。

### Java Code：

```java
class Solution {
    public int myAtoi(String str) {
        int offset = 0;
        boolean sign = false;
        int positive = 1;
        long num = 0;

        offset = getOffset(str);
        char[] arr = str.toCharArray();
        for (int i = offset; i < str.length(); ++i) {
            if (arr[i] == '+' || arr[i] == '-') {
                if (sign) return (int)(positive * num);
                sign = true;
                positive = arr[i] == '+' ? 1 : -1;
            } else if (arr[i] > '9' || arr[i] < '0') {
                return (int)(positive * num);
            } else {
                sign = true; // 用于处理数字后面接+-号的问题
                num = num * 10 + arr[i] - '0';
                if (positive > 0 && num > Integer.MAX_VALUE) {
                    return Integer.MAX_VALUE;
                } else if (positive < 0 && -num < Integer.MIN_VALUE) {
                    return Integer.MIN_VALUE;
                }
            }
        }
        return (int)(positive * num);
    }

    private int getOffset(String str) {
        int i = 0;
        while (i < str.length() && str.charAt(i) == ' ') {
            i++;
        }
        return i;
    }
}
```

