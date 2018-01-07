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
    public class Solution {
        /**
         * @param source a source string
         * @param target a target string
         * @return an integer as index
         */
        public int strStr2(String source, String target) {
            if (source == null || target == null) return -1;
            if (target.length() == 0) return 0;
            int BASE = (int)10e6;
            // 计算target的hashCode
            int pattern = 0;
            for (int i = 0; i < target.length(); ++i) {
                pattern = (pattern * 31 + target.charAt(i)) % BASE;
            }
            // 用sliding window的思路更新window内string的hashcode，和pattern比较
            int left = 0, right = 0;
            int window = 0; // window hashcode
            // 计算移除left时要减去的字符所乘系数
            int fact = 1;
            for (int i = 0; i < target.length(); ++i) {
                fact = (fact * 31) % BASE;
            }
            while (right < source.length()) {
                // 更新right
                window = (window * 31 + source.charAt(right)) % BASE;
                // 如有必要，移动left，维持window size
                if (right - left + 1 > target.length()) {
                    window = window - (source.charAt(left) * fact) % BASE;
                    if (window < 0) window += BASE;
                    left++;
                }
                // 判断
                if (window == pattern) {
                    if (source.substring(left, right + 1).equals(target)) {
                        return left;
                    }
                }
                // 移动right
                right++;
            }
            return -1;
        }
    }  
```