## Add Binary
_update Jan 19, 2018  17:30_

---
[LeetCode](https://leetcode.com/problems/add-binary/description/)

Given two binary strings, return their sum (also a binary string).

**For example,**

    a = "11"
    b = "1"
    Return "100".
    
<br>

### Basic Idea:
从右往左，逐位相加，保存 carrier，结果存入 StringBuilder，返回前 reverse。关键注意这里coding的框架，算是一个类似相加题目的范例吧。
* #### 教科书似的 Java Code:
```java
class Solution {
    public String addBinary(String a, String b) {
        StringBuilder sb = new StringBuilder();
        int i = a.length() - 1;
        int j = b.length() - 1;
        int carrier = 0;
        while (i >= 0 || j >= 0 || carrier > 0) {
            int n1 = i < 0 ? 0 : a.charAt(i--) - '0';
            int n2 = j < 0 ? 0 : b.charAt(j--) - '0';
            int sum = n1 + n2 + carrier;
            int digit = sum % 2;
            carrier = sum / 2;
            sb.append(digit);
        }
        return sb.reverse().toString();
    }
}
```

* #### C++ Code:
```cpp
    class Solution {
    public:
        string addBinary(string a, string b) {
            string ret;
            int i{a.size() - 1};
            int j{b.size() - 1};
            int last = 0;
            while (i >= 0 || j >= 0 || last > 0) {
                int add1 = i >=0 ? a[i] - '0' : 0;
                int add2 = j >=0 ? b[j] - '0' : 0;
                int curr = add1 + add2 + last;
                last = curr / 2;
                ret.append(string(1, curr % 2 + '0'));
                i--;
                j--;
                
            } 
            reverse(ret.begin(), ret.end());
            return ret;
        }
    };
```