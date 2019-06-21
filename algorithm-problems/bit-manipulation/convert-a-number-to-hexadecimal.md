# Convert a Number to Hexadecimal

_update Jan 13,2018 20:17_

[LeetCode](https://leetcode.com/problems/convert-a-number-to-hexadecimal/description/)

Given an integer, write an algorithm to convert it to hexadecimal. For negative integer, two’s complement method is used.

**Note:**

1. All letters in hexadecimal \(a-f\) must be in lowercase.
2. The hexadecimal string must not contain extra leading 0s. If the number is zero, it is represented by a single zero character '0'; otherwise, the first character in the hexadecimal string will not be the zero character.
3. The given number is guaranteed to fit within the range of a 32-bit signed integer.
4. You must not use any method provided by the library which converts/formats the number to hex directly.

**Example 1:**

```text
Input:
26

Output:
"1a"
```

**Example 2:**

```text
Input:
-1

Output:
"ffffffff"
```

## Basic Idea

基本思路就是用一个长度为 16 的 string 数组来map每一对 `<number, char>`，然后四位四位读 input integer，按顺序append到stringbuilder中。

### Java Code:

```java
    class Solution {
        public String toHex(int num) {
            if (num == 0) return "0";
            char[] hex = "0123456789abcdef".toCharArray();
            int mask = 0b1111;
            StringBuilder sb = new StringBuilder();
            while (num != 0) {
                int digit = num & mask;
                num >>>= 4;
                sb.append(hex[digit]);
            }
            return sb.reverse().toString();
        }
    }
```

