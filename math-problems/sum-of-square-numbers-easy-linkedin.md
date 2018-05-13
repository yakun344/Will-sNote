# Sum of Square Numbers
_update May 12,2018  21:42_

---
[LeetCode](https://leetcode.com/problems/sum-of-square-numbers/description/)


Given a non-negative integer c, your task is to decide whether there're two integers a and b such that `a^2 + b^2 = c`.

**Example 1:**
    
    Input: 5
    Output: True
    Explanation: 1 * 1 + 2 * 2 = 5

**Example 2:**

    Input: 3
    Output: False
    
<br>

### Basic Idea:
枚举第一个数 a，然后判断第二个数 `c - a^2` 是否是一个完全平方数，时间复杂度为 `O(sqrt(N))`。

* #### C++ Code：
```cpp
    typedef long long ll;
    
    class Solution {
    public:
        bool judgeSquareSum(int c) {
            for (ll i = 0; i * i <= c; ++i) {
                int t = c - i * i;
                int sq = (int)sqrt(t);
                if (sq * sq == t) return true;
            }
            return false;
        }
    };
```
