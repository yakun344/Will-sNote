## Add Binary
_update Jun 29, 2017_

---
[leetcode](https://leetcode.com/problems/add-binary/#/description)
Given two binary strings, return their sum (also a binary string).

For example,

    a = "11"
    b = "1"
Return "100".

#### 思路：
解决这种字符串形式的加法，可以使用这里的code做模板，比较concise。

#### Java code：
```java
    public class Solution {
        public String addBinary(String a, String b) {
            char[] arr1 = a.toCharArray();
            char[] arr2 = b.toCharArray();
            StringBuilder sb = new StringBuilder();
            int carrier = 0;
            int i = arr1.length - 1;
            int j = arr2.length - 1;
            while (i >= 0 || j >= 0 || carrier > 0) {
        // 用 -‘0’处理 char->int，用3目运算符处理不一样的长度
                int n1 = i >= 0 ? arr1[i--] - '0' : 0;
                int n2 = j >= 0 ? arr2[j--] - '0' : 0;
                int result = n1 + n2 + carrier;
                int d = result % 2;
                carrier = result / 2;
                sb.append(d + "");
            }
            return sb.reverse().toString();
        }
    }
```