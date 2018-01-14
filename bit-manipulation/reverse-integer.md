## Reverse Integer
_update Jan 13,2018  20:04_

---
[LeetCode](https://leetcode.com/problems/reverse-integer/description/)

Given a 32-bit signed integer, reverse digits of an integer.

**Example 1:**

    Input: 123
    Output:  321

**Example 2:**

    Input: -123
    Output: -321
    
**Example 3:**

    Input: 120
    Output: 21

**Note:**

Assume we are dealing with an environment which could only hold integers within the 32-bit signed integer range. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

#### Basic Idea:
每次取出 the least significant digit, 然后 `ret = ret * 10 + digit`。注意 int32 的溢出，所以要将 ret 声明为 long， 同时每次做判断： `if (ret != (int)ret) return 0;` 。

#### Java Code:
```java
public class Solution {
    public int reverse(int x) {
        boolean negative = x < 0;
        x = Math.abs(x);
        long ret = 0;
        while (x > 0) {
            ret = ret * 10 + x % 10;
            if (ret != (int)ret) return 0;
            x /= 10;
        }
        return negative ? -(int)ret : (int)ret;
    }
}
```