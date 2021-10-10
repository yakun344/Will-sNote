# Bit Manipulation Notes

_update Jan 13,2018 13:18_

## 1. [Power of Two](https://leetcode.com/problems/power-of-two/description/)

判断一个数是不是 2 的乘方，只要判断其二进制表示中有几个 1 即可：

  **第一种实现**

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

  **第二种实现 (更快)**

```python
class Solution(object):
    def isPowerOfTwo(self, n):
        """
        :type n: int                      # 例如 16：0b0001 0000 
        :rtype: bool                      #   16-1 =  0000 1111
        """                               # 只要是二的乘方，则一定满足 n & (n-1) == 0
        return False if n <= 0 else n & (n - 1) == 0
```

## 2. Count the Number of Bits That Are Different

[leetcode: Hamming Distance](https://leetcode.com/problems/hamming-distance/description/)\
只要 `countBit(a ^ b)` 就可以了。xor 可以得到所有两个数不同的位都为 1 的一个数，然后计算这个数中 1 的个数。 \


## 3. Determine If a String Contains Unique Characters

之前的思路是用一个HashSet，或者用一个 `int[26]`。用 bit manipulation 的思路，也可以直接用一个 int 的每个 bit 来表示一个字母。如果所面对的不只是26长度的字母，而是长度为256的ASCII码，则可以用一个 `int[8] bitVector`, 相当于把 256 个 bit 分成 8 个 group，每个 integer 代表 32 个 bits；

```java
    // 判断输入 word 是否只包含 unique 的 ASCII 字符。
    public class Solution {
      public boolean allUnique(String word) {
        int[] bitVector = new int[8];
        for (char c : word.toCharArray()) {
          int group = c / 32;
          int index = c % 32;
          if ((bitVector[group] >> index & 1) == 1) return false;
          else bitVector[group] |= (1 << index);
        }
        return true;
      }
    }
```

## 4. Reverse Integer

[LeetCode: Reverse Bits](https://leetcode.com/problems/reverse-bits/description/)\
两种思路，一种是用两个指针指向左右，如果左右不同则建一个只有这两位为 1 的 mask，然后与其 xor，例如：

```
    input:                1001 0110
    对于第 0 位和 -1 位:    l->   <-r
    两位不同，生成mask：     1000 0001
    XOR:                  0001 0111
    然后 l 和 r 向中间逼近
```

另一种思路是使用 `divide & conquer` 的思路，左右16位换位，然后每边左右8位换，然后每边4位换，然后每边2位换，这样时间复杂度 `O(log32) = 5` 更快；

```java
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        n = ((n & 0xffff0000) >>> 16) | ((n & 0x0000ffff) << 16); 
        n = ((n & 0xff00ff00) >>> 8) | ((n & 0x00ff00ff) << 8); 
        n = ((n & 0xf0f0f0f0) >>> 4) | ((n & 0x0f0f0f0f) << 4);
        n = ((n & 0xcccccccc) >>> 2) | ((n & 0x33333333) << 2);
        n = ((n & 0xaaaaaaaa) >>> 1) | ((n & 0x55555555) << 1);
        return n;
    }
}
```
