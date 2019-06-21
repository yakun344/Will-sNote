# Longest Palindromic Substring \(Medium\)

_update Feb 17, 2018 15:39_

[LeetCode](https://leetcode.com/problems/longest-palindromic-substring/description/)

Given a string s, find the longest palindromic substring in s. You may assume that the maximum length of s is 1000.

**Example:**

```text
Input: "babad"

Output: "bab"

Note: "aba" is also a valid answer.
```

**Example:**

```text
Input: "cbbd"

Output: "bb"
```

## Basic Idea:

基本思想就是对于每个可能的位置，从它开始向两边 extend。最高时间复杂度为 `O(n^2)`。可以写一个helper function，传入两个参数表示左边中点和右边中点，当传入左右中点相同的时候，表示奇书情况。

**优化：** 不再每次helper function中直接返回找到的 String，而是更改全局变量 start 和 maxLen，可以节省一部分时间。

* **Java Code:**

  ```java
    class Solution {
        private int start = 0, maxLength = 0;
        public String longestPalindrome(String s) {
            if (s == null) return "";
            else if (s.length() < 2) return s;

            for (int i = 0; i < s.length(); ++i) {
                helper(s, i, i); // odd
                helper(s, i - 1, i); // even
            }
            return s.substring(start, start + maxLength);
        }

        private void helper(String s, int l, int r) {
            while (l >= 0 && r < s.length() && s.charAt(l) == s.charAt(r)) {
                if (r - l + 1 > maxLength) {
                    start = l;
                    maxLength = r - l + 1;
                }
                l--;
                r++;
            }
        }
    }
  ```

