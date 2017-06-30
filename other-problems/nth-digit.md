## Nth Digit
_update Jun 30, 2017 12:08_

---
[leetcode](https://leetcode.com/problems/nth-digit/#/description)
Find the nth digit of the infinite integer sequence 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ...

**Note:**
n is positive and will fit within the range of a 32-bit signed integer (n < 231).

Example 1:
        
        Input:
        3
        
        Output:
        3
        Example 2:
        
        Input:
        11
        
        Output:
        0
        
**Explanation:**
The 11th digit of the sequence 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ... is a 0, which is part of the number 10.

#### 思路：
利用1-9有一位，10-99有两位，以此类推往后面数，但是code的细节太多，要注意。

#### Java code：
```java
    // java 
    public class Solution {
        public int findNthDigit(int n) {
            long start = 1;
            int size = 1;
            long step = 9;
            long prob = 9;
            while (n > prob) {
                n -= prob;
                size += 1;
                step *= 10;
                start *= 10;
                prob = size * step;
            }
            long number = start + (n - 1) / size;
            int dig = (n - 1) % size;
            return (number + "").charAt(dig) - '0';
        }
    }
```