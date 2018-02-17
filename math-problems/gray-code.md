## Gray Code
_update Aug 12, 2017  18:47_

---
[LeetCode](https://leetcode.com/problems/gray-code/description/)

The gray code is a binary numeral system where two successive values differ in only one bit.

Given a non-negative integer n representing the total number of bits in the code, print the sequence of gray code. A gray code sequence must begin with 0.

**For example**
    
    given n = 2, return [0,1,3,2]. Its gray code sequence is:
    
    00 - 0
    01 - 1
    11 - 3
    10 - 2
    
**Note:**
For a given n, a gray code sequence is not uniquely defined.

**For example**

    [0,2,3,1] is also a valid gray code sequence according to the above definition.

For now, the judge is able to judge based on one instance of gray code sequence. Sorry about that.

#### Basic Idea:
Gray Code的定义可以在wiki上找到，其实并非如题意所说有其他形式，而是有固定顺序的。

**方法1：（找规律）**

如图：
![](/assets/WechatIMG16.jpg)

Python Code:
```python
    # 解法 1，找规律
    class Solution(object):
        def grayCode(self, n):
            """
            :type n: int
            :rtype: List[int]
            """
            res = []
            res.append(0)
            for i in range(n):
                addend = 1 << i
                size = len(res)
                for i in range(len(res) - 1, -1, -1):
                    res.append(res[i] | addend)
            return res
```

**方法2：（数学公式）**

事实上，第 i 个grayCode（0 based）就是 `(i >> 1) ^ i`，而 k 位共有 `1<<k` 个grayCode （1 based）. 所以gray code就是从 `(0>>1)^0 到 ((k-1)>>1)^(k-1)`。

Python Code:
```python
    # 解法 2，数学公式，第i个gray code为（i >> 1）^ i
    class Solution(object):
        def grayCode(self, n):
            """
            :type n: int
            :rtype: List[int]
            """
            res = []
            num = 1 << n  # 例如当n=3时，一共有2^3=8个gray code，1<<3=8
            for i in range(num):
                res.append((i >> 1) ^ i)
            return res
```




