# Robin Karp Algorithm

_update Jul 23, 2017 23:39_

## Basic Idea:

利用计算字符串的HashCode进行线性时间匹配。

**具体地：**

HashCode 计算方法： 例如对于字符串“abc”，其hashcode即为 `(31^2 * a + 31^1 * b + 21^0 * c) % 1e6`，其中 1e6 为 BASE，31 为 magic number。

我们先计算 target 的 hashcode，然后用 source 中相同长短子字符串的hashcode与其相比，既可以过滤掉绝大多数不匹配的情况。具体实现中，可以想象用一个长度等于 len\(target\) 的 window 从左向右移动，每次加入右边字符，减去左边字符，然后和 targetCode比较。

使用left和right指针标志window的边界，然后用while循环实现比较简单。

## Java Code:

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

## C++ Code

```cpp
    #define BASE 1000000;

    class Solution {
    public:
        int strStr(string haystack, string needle) {
            if (needle.size() == 0) return 0;
            // get hash code for needle first
            int needle_hash = 0;
            for (auto& c : needle) {
                needle_hash = (needle_hash * 31 + c % 31) % BASE;
            }
            cout << needle_hash << endl;
            int factor = 1;
            for (int i = 0; i < needle.size() - 1; ++i) {
                factor = factor * 31 % BASE;
            }

            // sliding window
            int window_hash = 0;
            int left = 0, right = -1;
            while (true) {
                while (right - left + 1 < needle.size()) {
                    right++;
                    if (right == haystack.size()) {
                        return -1;
                    }
                    window_hash = (window_hash * 31 + haystack[right] % 31) % BASE; 
                }
                cout << window_hash << endl;

                if (window_hash == needle_hash && needle == haystack.substr(left, needle.size())) {
                    return left;
                }
                // move left 
                window_hash = window_hash - (haystack[left] % 31 * factor) % BASE;
                if (window_hash < 0) window_hash += BASE;
                left++;
            }
        }
    };
```

## Addendum

1. 要特别注意右移 left pointer 缩小了 window 之后，将最左边字符产生的影响移出 windowCode 的方式：

   ```java
    windowCode = windowCode - (leftChar % 31 * factor) % BASE;
    if (windowCode < 0) windowCode += BASE;
   ```

2. 注意求 factor 的时候，循环次数为 window size - 1，因为第一个字符不需要 `* 31`;

