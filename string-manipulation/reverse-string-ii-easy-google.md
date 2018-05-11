# Reverse String 
_update May 11,2018 16:22_

---
[LeetCode](https://leetcode.com/problems/reverse-string-ii/description/)

Given a string and an integer k, you need to reverse the first k characters for every 2k characters counting from the start of the string. If there are less than k characters left, reverse all of them. If there are less than 2k but greater than or equal to k characters, then reverse the first k characters and left the other as original.

**Example:**

    Input: s = "abcdefg", k = 2
    Output: "bacdfeg"

**Restrictions:**

* The string consists of lower English letters only.
* Length of the given string and k will in the range `[1, 10000]`.

<br>

### Basic Idea:
每 2k 个char，reverse前 k 个。只要写一个 helper function 来 reverse k 个 char，然后用一个指针每次跳 2k 个位置就可以了。

* #### C++ Code:
```cpp
    class Solution {
        // reverse k characters from start，如果长度不足k，则都reverse
        void reverseK(string& s, int start, int k) {
            int end = start + k - 1 < s.size() ? start + k - 1 : s.size() - 1; 
            while (start < end) {
                swap(s[start++], s[end--]);
            }
        }
    public:
        string reverseStr(string s, int k) {
            if (s.size() <= 1) return s;
            int idx = 0;
            while (idx < s.size()) {
                reverseK(s, idx, k);
                idx += 2 * k;
            }
            return s;
        }
    };
```