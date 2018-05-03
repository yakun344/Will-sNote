## Roman to Integer
_update May 3,2018  15:34_

---
[LeetCode](https://leetcode.com/problems/roman-to-integer/description/)

Roman numerals are represented by seven different symbols: I, V, X, L, C, D and M.

    Symbol       Value
    I             1
    V             5
    X             10
    L             50
    C             100
    D             500
    M             1000
**For example**, two is written as II in Roman numeral, just two one's added together. Twelve is written as, XII, which is simply X + II. The number twenty seven is written as XXVII, which is XX + V + II.

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not IIII. Instead, the number four is written as IV. Because the one is before the five we subtract it making four. The same principle applies to the number nine, which is written as IX. There are six instances where subtraction is used:

I can be placed before V (5) and X (10) to make 4 and 9.  
X can be placed before L (50) and C (100) to make 40 and 90.   
C can be placed before D (500) and M (1000) to make 400 and 900.  
Given a roman numeral, convert it to an integer. Input is guaranteed to be within the range from 1 to 3999.  

**Example 1:**

    Input: "III"
    Output: 3
    Example 2:
    
    Input: "IV"
    Output: 4
    Example 3:
    
    Input: "IX"
    Output: 9
    Example 4:
    
    Input: "LVIII"
    Output: 58
    Explanation: C = 100, L = 50, XXX = 30 and III = 3.
    Example 5:
    
    Input: "MCMXCIV"
    Output: 1994
    Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.

<br>
        
### Basic Idea:
根据罗马数字"小数左减右加"的规律: 例如对于 4 ，`IV`，I 小于 V，I 在左边，则值是 `5-1=4`, 而对于 `VII`, I 小于 V，在右边，则是 `5+1+1 = 7`。所以我们只要从左到右遍历 string，将相应的字母对应的数字存入一个数组中，然后从左到右遍历该数组，如果`num[i+1]>num[i]`, 则 `sum-=num[i]`, 否则 `sum+=num[i]`;

### Java Code：
```java
class Solution {
    public int romanToInt(String s) {
        if (s.length() == 0) return 0;
        int[] num = new int[s.length()];
        for (int i = 0; i < s.length(); ++i) {
            char c = s.charAt(i);
            switch (c) {
                case ('I'): num[i] = 1; break; 
                case ('V'): num[i] = 5; break; 
                case ('X'): num[i] = 10; break; 
                case ('L'): num[i] = 50; break;
                case ('C'): num[i] = 100; break;
                case ('D'): num[i] = 500; break;
                case ('M'): num[i] = 1000; break;
            }
        }
        int sum = 0;
        for (int i = 0; i < num.length - 1; ++i) {
            if (num[i] < num[i + 1]) {
                sum -= num[i];
            } else {
                sum += num[i];
            }
        }
        return sum + num[num.length - 1];
    }
}
```

### C++ Code:
```cpp




















