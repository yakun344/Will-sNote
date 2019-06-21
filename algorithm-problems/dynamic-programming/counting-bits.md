# Counting Bits

_update Jul3, 2017 11:13_

[leetcode](https://leetcode.com/problems/counting-bits/#/description)

Given a non negative integer number num. For every numbers i in the range 0 ≤ i ≤ num calculate the number of 1's in their binary representation and return them as an array.

```text
Example:
For num = 5 you should return [0,1,1,2,1,2].
```

**Follow up:**

It is very easy to come up with a solution with run time O\(n\*sizeof\(integer\)\). But can you do it in linear time O\(n\) /possibly in a single pass? Space complexity should be O\(n\). Can you do it like a boss? Do it without using any builtin function like `__builtin_popcount` in c++ or in any other language.

## Basic Idea:

仔细观察二进制数的规律就会发现有如下关系：

```text
`dp[i] = dp[i / 2] when i % 2 == 0`

`else dp[i] = dp[i / 2] + 1`
```

利用这点性质，我们就可以得出我们的 O\(n\) dp 算法。

## Python Code：

```python
    class Solution(object):
        def countBits(self, num):
            """
            :type num: int
            :rtype: List[int]
            """
            res = [0] * (num + 1)
            for i in range(1, len(res)):
                res[i] = res[i / 2] if i % 2 == 0 else res[i / 2] + 1
            return res
```

