# Reverse Integer

_update Jan 13,2018 20:04_

[LeetCode](https://leetcode.com/problems/reverse-integer/description/)

Given a 32-bit signed integer, reverse digits of an integer.

**Example 1:**

```text
Input: 123
Output:  321
```

**Example 2:**

```text
Input: -123
Output: -321
```

**Example 3:**

```text
Input: 120
Output: 21
```

**Note:**

Assume we are dealing with an environment which could only hold integers within the 32-bit signed integer range. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

## Basic Idea:

每次取出 the least significant digit, 然后 `ret = ret * 10 + digit`。注意 int32 的溢出，所以要将 ret 声明为 long， 同时每次做判断： `if (ret != (int)ret) return 0;` 。

## Java Code:

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

## C++ Code:

```cpp
typedef long long ll;

class Solution {
public:
    int reverse(int x) {
        int sign = x > 0 ? 1 : -1;
        ll n = abs((ll)x);
        ll res = 0;
        while (n > 0) {
            res = res * 10 + n % 10;
            n /= 10;
        }
        res *= sign;

        // 0x80000000 会被认为是 ul 类型，所以要转换为int才是 Integer.MIN_VALUE
        if (res < int(0x80000000) || res > 0x7fffffff) {
            return 0;
        } else {
            return (int)res;
        }
    }
};
```

