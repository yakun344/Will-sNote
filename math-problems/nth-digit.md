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
    //java
    public class Solution {
        public int findNthDigit(int n) {
            int size = 1;
            long step = 9;
            long prob = 9;
            int start = 1;
            while (n > prob) {
                size += 1;
                step *= 10;
                start *= 10;
                n -= prob;
                prob = step * size;
            }
            long number = start + (n - 1) / size;
            int pos = (n - 1) % size;
            return (number + "").charAt(pos) - '0';
        }
    }
```

---
_update May 8,2018_

### 新思路

和之前的想法其实类似，都是先算出之前的数字个数，然后算该第n个数字所出现在的那个数，然后找到第n个数。

* #### Java
```java
class Solution {
    public int findNthDigit(int n) {
        if (n <= 9) return n;
        int step = 0;
        int count = 0;
        int prevCount = 0;
        while (count < n) {
            prevCount = count;
            count += (Math.pow(10, step + 1) - Math.pow(10, step)) * (step + 1);
            step++;
        }
        
        // 例如 1-9 有9个，10-99 有90*2个，100-999有900*3个...
                                
        int numPos = (n - prevCount + 1) / step;   
        
        int num = (int)Math.pow(10, step - 1) + numPos - 1;
        
        System.out.println(num);
        
        int pos = (n - 1 - prevCount) % step;
        return String.valueOf(num).charAt(pos) - '0';
    }
}
```