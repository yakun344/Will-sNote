# Reverse Bits

_update Jun 30, 2017 17:56_

[leetCode](https://leetcode.com/problems/reverse-bits/#/description)  
Reverse bits of a given 32 bits unsigned integer.

For example, given input **43261596** \(represented in binary as **00000010100101000001111010011100**\), return **964176192** \(represented in binary as **00111001011110000010100101000000**\).

**Follow up:** If this function is called many times, how would you optimize it?

Related problem: [Reverse Integer](https://leetcode.com/problems/reverse-integer/)

## 思路：

1. 首先是利用位运算和循环，每次移动一位，移动32次完成reverse，但是这样效率比较低。
2. 因为输入的int是固定32位的，我们可以手动实现 divide and conquer 的算法，先把输入int折半reverse，再把每部分折半reverse。

## with loop， java code：

```java
    // java
    public class Solution {
        // you need treat n as an unsigned value
        public int reverseBits(int n) {
            int res = 0;
            for (int i = 0; i < 32; ++i) {
                res += n & 1;
                n >>>= 1;
                if (i < 31) res <<= 1;
            }
            return res;
        }
    }
```

## divide and conquer, java code:

```java
    // java
    public class Solution {
        // you need treat n as an unsigned value
        public int reverseBits(int n) {
            n = ((n & 0xffff0000) >>> 16) | ((n & 0x0000ffff) << 16); // abcd efgh ->efgh abcd
            n = ((n & 0xff00ff00) >>> 8) | ((n & 0x00ff00ff) << 8); // -> ghef cdab
            n = ((n & 0xf0f0f0f0) >>> 4) | ((n & 0x0f0f0f0f) << 4);
            n = ((n & 0xcccccccc) >>> 2) | ((n & 0x33333333) << 2);
            n = ((n & 0xaaaaaaaa) >>> 1) | ((n & 0x55555555) << 1);
            return n;
        }
    }
```

