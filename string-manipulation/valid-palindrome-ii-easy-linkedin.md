# Valid Palindrome II
_update May 13,2018  13:20_

---
[LeetCode](https://leetcode.com/problems/valid-palindrome-ii/description/)


Given a non-empty string s, you may delete at most one character. Judge whether you can make it a palindrome.

**Example 1:**

    Input: "aba"
    Output: True

**Example 2:**

    Input: "abca"
    Output: True
    Explanation: You could delete the character 'c'.

**Note:**  
The string will only contain lowercase characters a-z. The maximum length of the string is 50000.

<br>

### Basic Idea 
使用left和right两个指针从左右两端向中间逼近，判断是否相等，如果遇到不等的情况，则分别考虑去掉left和去掉right之后能否继续组成回文序列，如果都不行，则返回false；

* #### C++ Code:
```cpp
    class Solution {
        bool helper(string s, int left, int right) {
            while (left < right) {
                if (s[left] != s[right]) return false;
                left++; 
                right--;
            }
            return true;
        }
    public:
        bool validPalindrome(string s) {
            int left = 0, right = s.size() - 1;
            while (left < right) {
                if (s[left] != s[right]) {
                    return helper(s, left + 1, right) || helper(s, left, right - 1);
                } else {
                    left++;
                    right--;
                }
            }
            return true;
        }
    };
```