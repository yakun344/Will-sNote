## Bit Manipulation Notes
_update Jan 13,2018  13:18_

---
#### 1. [Power of Two](https://leetcode.com/problems/power-of-two/description/)
判断一个数是不是 2 的乘方，只要判断其二进制表示中有几个 1 即可：

&emsp; **第一种实现**  
```python
class Solution(object):
    def isPowerOfTwo(self, n):
        """
        :type n: int
        :rtype: bool
        """
        if n <= 0:                 # 先特判 0 和 负数，然后计数 1 的个数
            return False           # 如果 1 的个数大于 1，直接返回 False
        numberOfOne = 0
        while n > 0:
            numberOfOne += n & 1
            n >>= 1
            if numberOfOne > 1:
                return False
        return True
```

&emsp; **第二种实现 (更快)**
```python
class Solution(object):
    def isPowerOfTwo(self, n):
        """
        :type n: int                      # 例如 16：0b0001 0000 
        :rtype: bool                      #   16-1 =  0000 1111
        """                               # 只要是二的乘方，则一定满足 n & (n-1) == 0
        return False if n <= 0 else n & (n - 1) == 0
```
<br>
#### 2. Count the Number of Bits That Are Different
[leetcode: Hamming Distance](https://leetcode.com/problems/hamming-distance/description/)  
只要 `countBit(a ^ b)` 就可以了。xor 可以得到所有两个数不同的位都为 1 的一个数，然后计算这个数中 1 的个数。
<br>
#### 3. Determine If a String Contains Unique Characters
之前的思路是用一个HashSet，或者用一个 `int[26]`。用 bit manipulation 的思路，也可以直接用一个 int 的每个 bit 来表示一个字母。  
```java






























