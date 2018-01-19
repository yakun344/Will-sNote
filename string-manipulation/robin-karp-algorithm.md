## Robin Karp Algorithm
_update Jul 23, 2017 23:39_

---
#### Basic Idea:
利用计算字符串的HashCode进行线性时间匹配。

**具体地：**

HashCode 计算方法：
例如对于字符串“abc”，其hashcode即为 `(31^2 * a + 31^1 * b + 21^0 * c) % 1e6`，其中 1e6 为 BASE，31 为 magic number。

我们先计算 target 的 hashcode，然后用 source 中相同长短子字符串的hashcode与其相比，既可以过滤掉绝大多数不匹配的情况。具体实现中，可以想象用一个长度等于 len(target) 的 window 从左向右移动，每次加入右边字符，减去左边字符，然后和 targetCode比较。

使用left和right指针标志window的边界，然后用while循环实现比较简单。

#### Java Code:
```java
class Solution {
    private final int BASE = 1000000;
    public int strStr(String haystack, String needle) {
        if (haystack == null || needle == null) return -1;
        else if (needle.length() == 0) return 0;
        
        int targetCode = getHashCode(needle);
        int factor = getFactor(needle.length());
        
        // sliding window
        int left = 0, right = -1, windowCode = 0;
        while (right < haystack.length()) {
            while (right - left + 1 < needle.length()) {
                right++;
                if (right == haystack.length()) return -1;
                windowCode = (windowCode * 31 + haystack.charAt(right) % 31) % BASE;
            }
            if (windowCode == targetCode) {
                if (haystack.substring(left, right + 1).equals(needle)) return left;
            }
            // move left pointer
            windowCode = windowCode - (haystack.charAt(left) % 31 * factor) % BASE;
            if (windowCode < 0) windowCode += BASE;
            left++;
        }
        return -1;
    }
    
    // calculate the hashcode of needle
    private int getHashCode(String target) {
        int code = 0;
        for (int i = 0; i < target.length(); ++i) {
            code = (code * 31 + target.charAt(i) % 31) % BASE;
        }
        return code;
    }
    
    // calculate the factor of the leftmost character in the window
    private int getFactor(int windowSize) {
        int factor = 1;
        for (int i = 0; i < windowSize - 1; ++i) {
            factor = factor * 31 % BASE;
        }
        return factor;
    }
}
```