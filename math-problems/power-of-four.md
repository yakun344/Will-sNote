# Power of Four
_update Jan 26,2018 11:21_

---
[LeetCode](https://leetcode.com/problems/power-of-four/description/)

Given an integer (signed 32 bits), write a function to check whether it is a power of 4.

**Example:**

    Given num = 16, return true. Given num = 5, return false.

**Follow up:** Could you solve it without loops/recursion?

<br>

## basic Idea:
* ### 思路1：loop
写一个while loop，每次乘 4，直到和 num 相等或者大于 num；

* ### 思路2：枚举
因为 Integer 范围内的 4 的乘方一共其实只有 `[1, 4, 16, 64, 256, 1024, 4096, 16384, 65536, 262144, 1048576, 4194304, 16777216, 67108864, 268435456, 1073741824]` 这16个数，可以把他们放进一个 HashSet，然后判断；

* ### 思路3：位运算
我们知道如果一个 int 的二进制表示中只有一个 1，那么它一定是 2 的乘方，例如 `1, 2(10), 4(100), 8(1000)`, 而且我们注意到如果一个数是 4 的乘方，它一定是 2 的乘方，并且和 2 的乘方数之间是有规律可寻的，如下图：
```c
    2 的乘方一定是只含有一个 1， 而 4 的乘方一定是 2 的乘方：
        1            1         1 
        2           10         x
        4          100         100 
        8         1000         x 
       16       1 0000         1 0000
       32      10 0000         x
       64     100 0000         100 0000
       ...
    可以看到如下规律，在 1，2，4，8，16，32，64 ... 中，
                 只有 1，   4，   16，   64 ... 是 4 的乘方，
                 所以我们可以用一个 bitmask：0x55555555, 
                 即 '0b1010101010101010101010101010101' 来筛选2的乘方中的4的乘方；
```     
  * #### Java Code:
  ```java
    class Solution {
        public boolean isPowerOfFour(int num) {
            if ((num & (num - 1)) == 0 && (num & 0x55555555) != 0) {
                return true;
            } else {
                return false;
            }
        }
    }
  ```
  
  
  
  
  
  
  



